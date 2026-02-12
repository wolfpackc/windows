#  Ver el contenido de un ADS

### CMD

```cmd
more < archivo.txt:oculto.txt
```

o

```cmd
type archivo.txt:oculto.txt
```

---

### PowerShell

```powershell
Get-Content archivo.txt -Stream oculto.txt
```

---

# ✏️ Crear un ADS (para pruebas)

CMD:

```cmd
echo HOLA > prueba.txt:secreto.txt
```

PowerShell:

```powershell
Set-Content prueba.txt -Stream secreto.txt -Value "HOLA"
```

---

#  Eliminar ADS

PowerShell:

```powershell
Remove-Item prueba.txt -Stream secreto.txt
```

Eliminar TODOS los streams ocultos:

```powershell
Get-ChildItem . -Recurse |
ForEach-Object {
  Remove-Item $_.FullName -Stream * -ErrorAction SilentlyContinue
}
```
