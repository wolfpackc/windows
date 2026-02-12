
#  Idea base

Los **ADS (Alternate Data Streams)** son una característica **exclusiva del sistema de archivos NTFS**.

Si un archivo sale de NTFS y pasa por un sistema de archivos que **no soporta ADS**, esos streams:

👉 **se pierden**


#  Qué ocurre al copiar a un USB típico

Muchos pendrives vienen formateados en:

* FAT32
* exFAT

Ambos **no soportan ADS**.

Entonces:

1. Archivo en NTFS con Zone.Identifier
2. Lo copias al USB (FAT32/exFAT)
3. El ADS desaparece
4. Copias de vuelta al disco NTFS

Resultado:

👉 Archivo SIN Zone.Identifier

👉 Windows lo ve como local

Después de ese “lavado”:

* SmartScreen normalmente NO aparece
* 
* El archivo no figura como descargado de Internet

---

#  Matiz extra interesante

También se pierden ADS cuando:

* Se comprime en ZIP y se descomprime con algunas herramientas
* Se envía por ciertos sistemas de correo
* Se copia vía SMB a sistemas no-NTFS
