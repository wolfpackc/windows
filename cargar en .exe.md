## Cómo el Loader de Windows Procesa la Cabecera PE al Cargar un .exe

Cuando ejecutas un `.exe`, **no es el archivo el que "se carga solo"** — es el **Windows Loader** (implementado principalmente en `ntdll.dll` y el kernel `ntoskrnl.exe`) quien lee, parsea y prepara el ejecutable en memoria. Aquí está el flujo detallado:

---

### 🔹 Fase 1: Inicio del Proceso (`CreateProcess` → `NtCreateUserProcess`)

```c
// User mode (kernel32.dll)
CreateProcess("app.exe", ...) 
    → NtCreateUserProcess(...)          // syscall a kernel mode
```

El kernel crea la estructura del proceso vacío (`_EPROCESS`) y su primer hilo (`_ETHREAD`), **pero aún no hay código del .exe en memoria**.

---

### 🔹 Fase 2: Mapeo del Archivo PE (`NtMapViewOfSection`)

El loader **no lee el archivo secuencialmente con `ReadFile`** — usa **memory-mapped I/O**:

```c
// Kernel mode (ntoskrnl.exe)
MiCreateImageFileObject()      // abre el archivo como objeto sección
MiMapViewOfSection()           // mapea las cabeceras PE a memoria virtual
```

✅ **Solo las primeras páginas del archivo** (donde están DOS Header + PE Header + Section Headers) se mapean inicialmente.  
✅ El resto del archivo se carga **bajo demanda** (page fault) cuando se accede.

---

### 🔹 Fase 3: Parseo de la Cabecera PE (User Mode - `ntdll.dll`)

Una vez mapeadas las cabeceras, el loader en **user mode** (`ntdll.dll!LdrpInitializeProcess`) las parsea así:

| Paso | Acción | Estructura Accedida |
|------|--------|---------------------|
| 1 | Verifica `e_magic == "MZ"` | `IMAGE_DOS_HEADER` |
| 2 | Lee `e_lfanew` → salta al offset de la firma PE | `IMAGE_DOS_HEADER.e_lfanew` |
| 3 | Verifica `"PE\0\0"` (0x00004550) | Firma PE |
| 4 | Lee `IMAGE_FILE_HEADER` → arquitectura, nº secciones | `IMAGE_FILE_HEADER` |
| 5 | Lee `IMAGE_OPTIONAL_HEADER` → `ImageBase`, `AddressOfEntryPoint`, Data Directories | `IMAGE_OPTIONAL_HEADER` |
| 6 | Itera los `IMAGE_SECTION_HEADER` → prepara mapeo sección por sección | Array de `IMAGE_SECTION_HEADER` |

📌 **Clave**: El loader **no copia byte a byte** — configura las **tablas de páginas virtuales** para que cada sección apunte a su región en el archivo mapeado.

---

### 🔹 Fase 4: Carga de Secciones en Memoria

Para cada sección (`.text`, `.data`, `.rsrc`, etc.):

```c
VirtualAlloc(ImageBase + Section.VirtualAddress, 
             Section.VirtualSize, 
             MEM_COMMIT | MEM_RESERVE, 
             PAGE_EXECUTE_READWRITE);

memcpy(destino, 
       archivo_mapeado + Section.PointerToRawData, 
       Section.SizeOfRawData);

// Rellena con ceros el resto (VirtualSize > SizeOfRawData)
memset(resto, 0, VirtualSize - SizeOfRawData);
```

⚠️ **Si `ImageBase` está ocupado** → se dispara la **reubicación** (usando la tabla `.reloc` en `IMAGE_DIRECTORY_ENTRY_BASERELOC`).

---

### 🔹 Fase 5: Resolución de Imports

El loader recorre la **Import Address Table (IAT)**:

```c
// Para cada DLL en la tabla de imports:
LoadLibrary("kernel32.dll")          // carga la DLL si no está en memoria
GetProcAddress(hModule, "CreateFileW")  // resuelve RVA de la función
Escribe RVA en la IAT → el .exe ahora apunta a la función real
```

📍 Esto ocurre **antes** de que el código del .exe se ejecute.

---

### 🔹 Fase 6: Inicialización y Transferencia de Control

1. Ejecuta callbacks de TLS (`TLS Directory`)
2. Ejecuta `DllMain` de DLLs cargadas (con `DLL_PROCESS_ATTACH`)
3. **Salta al `AddressOfEntryPoint`** (RVA definido en Optional Header)

```asm
JMP [ImageBase + AddressOfEntryPoint]  ; ¡Tu código comienza a ejecutarse!
```

---

### 📊 Diagrama del Flujo del Loader

```
.exe en disco
     │
     ▼
[Memory Map] → Mapea cabeceras PE (solo primeras páginas)
     │
     ▼
Parsea: DOS Header → PE Signature → COFF → Optional Header → Section Headers
     │
     ▼
Reserva espacio virtual en ImageBase (o reubica si conflicto)
     │
     ▼
Mapea/copia cada sección (.text, .data, .rsrc...) según Section Headers
     │
     ▼
Aplica reubicaciones (si ImageBase != preferido)
     │
     ▼
Resuelve imports (IAT → GetProcAddress)
     │
     ▼
Inicializa TLS y llama a DllMain de dependencias
     │
     ▼
Salta a AddressOfEntryPoint → ¡Tu programa corre!
```

---

### 🔍 ¿Dónde reside el loader?

| Componente | Modo | DLL/Driver | Función |
|------------|------|------------|---------|
| `CreateProcessW` | User | `kernel32.dll` | API pública |
| `LdrpInitializeProcess` | User | `ntdll.dll` | Parseo PE, imports, TLS |
| `MiCreateImageFileObject` | Kernel | `ntoskrnl.exe` | Mapeo de secciones |
| `PspUserThreadStartup` | Kernel | `ntoskrnl.exe` | Inicia primer hilo en user mode |

---

### 💡 Observación Práctica

Puedes ver este proceso en vivo con **WinDbg**:

```bash
# Adjuntar al inicio de un proceso
windbg -o notepad.exe

# Puntos de ruptura útiles
bp ntdll!LdrpInitializeProcess   # inicio del parseo PE
bp ntdll!LdrpProcessWork         # resolución de imports
```
