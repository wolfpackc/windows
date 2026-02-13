# ES UN PUTO PUENTE
<img width="1226" height="313" alt="loc_scheme_kes11_amsi_algorithm" src="https://github.com/user-attachments/assets/3e8e73be-c840-4b4c-8839-f2bca5867593" />

---

¿Qué hace AMSI?

Permite a aplicaciones que ejecutan contenido dinámico (scripts) —como PowerShell, JavaScript, VBScript, macros de Office, etc.— enviar ese contenido a un escáner antimalware para revisión antes de ejecutarlo.

Cuando dichas aplicaciones (que deben estar diseñadas para usar AMSI) van a ejecutar código en tiempo de ejecución, pueden enviar ese código a AMSI primero, y AMSI lo pasa al motor antimalware registrado (por ejemplo, Microsoft Defender) para analizarlo.

Si el análisis determina que es malicioso, el motor puede bloquearlo o detener su ejecución.

❓ ¿Se analiza todo lo que se ejecuta en el sistema?

🔹 No exactamente.
AMSI no es un filtro universal que intercepte cada ejecutable (EXE, DLL, PDF, etc.) antes de que el sistema lo ejecute.

Solo interviene cuando una aplicación integrada decide usar AMSI para escanear algo —usualmente cuando ejecuta código o contenido dinámico (como scripts o macros).

## Por ejemplo:

PowerShell manda scripts a AMSI antes de ejecutarlos.

VBA o macros en Office pueden enviarse a AMSI antes.

Aplicaciones diseñadas para integrar AMSI pueden hacerlo explícitamente.

🔹 Pero un simple programa nativo como un juego o una app Win32 normal no pasa por AMSI automáticamente antes de ejecutarse, a menos que esa app esté programada para enviar algo a AMSI.

🖥️ ¿Se analiza un PDF antes de abrirlo?

AMSI no escanea directamente archivos PDF por sí mismo.

Sin embargo, si el lector de PDF —como Adobe Reader— ejecuta scripts o hace algo que se considera “contenido que puede ser peligroso”, esa parte podría integrarse con AMSI para escanearlo.
