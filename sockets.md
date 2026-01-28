Entender la idea de que existe un estandar C, y luego funciones de C que solo entienden ciertas APIs de X sistemas operativos.

Por ejemplo, con sockets, en C estandar no existe nada para crear sockets y que luego la libc sea capaz de traducirlo a la API correspondiente. 
Por lo tanto solo nos quedaria crear sockets mediante las funciones que ofrece cada API. La putada aqui viene con que no es multicompatible dado que se utilizan diferentes funciones según el sistema operativo.

No es el caso de printf donde es una funcion que si está en todas las libc por lo tanto , el compilador o linker (npi) es capaz de traducirlo a la funcion correspondiente de la API O S.O.


---

### 1️⃣ C estándar **no tiene sockets**

* No existe ninguna función de sockets en **C estándar** (`libc`).
* Todo lo que tiene que ver con redes depende del **sistema operativo**:

  * Linux → **POSIX sockets** (`socket()`, `bind()`, `listen()`, `accept()`)
  * Windows → **Winsock** (`WSASocket()`, `bind()`, `listen()`, `accept()`)

---

### 2️⃣ Ejemplo mínimo en **Linux**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int sock = socket(AF_INET, SOCK_STREAM, 0); // POSIX socket
    if (sock < 0) { perror("socket"); return 1; }

    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(8080);
    addr.sin_addr.s_addr = INADDR_ANY;

    if (bind(sock, (struct sockaddr*)&addr, sizeof(addr)) < 0) {
        perror("bind"); return 1;
    }

    printf("Servidor preparado en puerto 8080\n");
    close(sock);
    return 0;
}
```

* `socket`, `bind`, `close` → llamadas **directas a POSIX/Linux**, no parte de C estándar.
* `printf` → función **C estándar**, pasa por libc.

---

### 3️⃣ Ejemplo mínimo en **Windows (Winsock)**

```c
#include <winsock2.h>
#include <windows.h>
#include <stdio.h>

#pragma comment(lib, "ws2_32.lib") // Winsock

int main() {
    WSADATA wsa;
    WSAStartup(MAKEWORD(2,2), &wsa);

    SOCKET sock = socket(AF_INET, SOCK_STREAM, 0); // Winsock
    if (sock == INVALID_SOCKET) { printf("Error en socket\n"); return 1; }

    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(8080);
    addr.sin_addr.s_addr = INADDR_ANY;

    if (bind(sock, (struct sockaddr*)&addr, sizeof(addr)) == SOCKET_ERROR) {
        printf("Error en bind\n"); return 1;
    }

    printf("Servidor preparado en puerto 8080\n");
    closesocket(sock);
    WSACleanup();
    return 0;
}
```

* `WSAStartup`, `socket`, `bind`, `closesocket` → funciones **Win32/Winsock**, llamadas directas al SO
* `printf` → C estándar, traducido por libc (MSVCRT/UCRT)

---

### 🔹 Concepto general

* **C estándar (`printf`, `malloc`)** → portable, libc traduce a llamadas que el SO entiende
* **Sockets** → **no estándar**, dependen del SO: POSIX en Linux/macOS, Winsock en Windows

---
