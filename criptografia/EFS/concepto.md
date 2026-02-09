Cuando quieres cifrar un archivo con EFS:

Windows genera una clave de cifrado de archivo (FEK) aleatoria.

El archivo se cifra con AES usando esa FEK.

El resultado cifrado se guarda en el disco.

Para que el archivo pueda ser descifrado por el usuario autorizado, la FEK también se guarda, pero no en claro, sino cifrada con la clave pública del usuario (así nadie más puede usarla).

Cuando el usuario abre el archivo:

Windows usa la clave privada del usuario para descifrar la FEK.

Con la FEK descifrada, Windows descifra el contenido del archivo con AES.
