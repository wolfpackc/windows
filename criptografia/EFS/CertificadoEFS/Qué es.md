
---

## ✅ ¿Qué es un certificado EFS?

Un **certificado EFS (Encrypting File System Certificate)** es un **certificado digital X.509** que contiene un **par de claves asimétricas**:

* 🔓 **Clave pública**
* 🔐 **Clave privada**

Windows lo crea automáticamente la **primera vez que un usuario cifra un archivo con EFS**.

Ese certificado **identifica al usuario** y le permite:

👉 Cifrar la FEK (File Encryption Key)
👉 Descifrar la FEK cuando abre archivos cifrados

---

## 🎯 Para qué se usa exactamente

Cuando cifras un archivo con EFS:

1. Windows genera una FEK (simétrica)
2. La FEK cifra el archivo con AES
3. La FEK se cifra con la **clave pública del certificado EFS**
4. Se guarda junto al archivo

Cuando lo abres:

1. Windows usa la **clave privada del certificado EFS**
2. Descifra la FEK
3. La FEK descifra el archivo

---

## 📦 Dónde se guarda

### Certificado (parte pública)

En el almacén de certificados del usuario:

```
Current User
 └─ Personal
     └─ Certificates
```

### Clave privada

En disco, protegida por DPAPI:

```
%APPDATA%\Microsoft\Crypto\RSA\<SID>\
```

(la clave está cifrada)

---

## 🔐 Relación con DPAPI

* La **clave privada del certificado EFS** está cifrada con DPAPI
* DPAPI usa la **Master Key**
* La Master Key está protegida con una clave derivada de tu contraseña

---

