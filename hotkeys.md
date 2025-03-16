# 🖱️ Manual de Configuración de Mouse, Scroll y Movimiento de Ventanas por Teclado con AutoHotkey v2.0 (Chocolatey Edition)
**Versión: 1.0**  
**Autor:** [Tu Nombre]  
**Fecha:** 2025

---

## ✅ ¿Qué hace este sistema?
Permite mover el cursor del mouse, hacer clics, hacer scroll y mover ventanas a los snaps de Windows **usando solo el teclado**, sin necesidad de un mouse físico.  
Funciona en **Windows 11** con **AutoHotkey v2.0**, compatible con **múltiples monitores** de diferentes resoluciones y posiciones.

---

## ⚙️ Requisitos Previos

1. **Windows 11 (o 10)**  
2. **Chocolatey instalado**  
   - Si no lo tenés, seguí los pasos desde [https://chocolatey.org/install](https://chocolatey.org/install)
3. **AutoHotkey v2.0+** mediante Chocolatey  
4. Teclado funcional (aunque no tenga flechas)

---

## 🏗️ Pasos de Instalación

### 1. Instalar AutoHotkey v2.0 con Chocolatey

Abrí **PowerShell como Administrador** y ejecutá:
```powershell
choco install autohotkey --version=2.0.12
```
✅ Esto instalará la versión correcta de AutoHotkey v2.0.

---

### 2. Crear el Script de Control de Mouse, Scroll y Movimiento de Ventanas

#### 2.1. Abrir **Notepad** o tu editor favorito.

#### 2.2. Copiar el siguiente código y pegarlo en el editor:

```ahk
#Requires AutoHotkey v2.0
#SingleInstance Force

; Ctrl + Alt + H = Win + Left
^!h:: Send("#{Left}")

; Ctrl + Alt + L = Win + Right
^!l:: Send("#{Right}")

; Ctrl + Alt + K = Win + Up
^!k:: Send("#{Up}")

; Ctrl + Alt + J = Win + Down
^!j:: Send("#{Down}")

; Movimiento entre monitores (ya lo tenías perfecto)
step := 30

#+h:: MoveCursor("left")
#+l:: MoveCursor("right")
#+k:: MoveCursor("up")
#+j:: MoveCursor("down")

; Clicks
#+u:: Click("left")
#+o:: Click("right")

; SCROLL DEFINITIVO
#+m:: Send("{WheelUp 3}")   ; Scroll arriba
#+n:: Send("{WheelDown 3}") ; Scroll abajo

; ---- Movimiento de cursor (manteniendo lo que ya funciona) ----
MoveCursor(direction) {
    global monitors, virtual_left, virtual_top, virtual_right, virtual_bottom, step

    MouseGetPos(&curX, &curY)

    newX := curX
    newY := curY

    if (direction = "left")
        newX -= step
    else if (direction = "right")
        newX += step
    else if (direction = "up")
        newY -= step
    else if (direction = "down")
        newY += step

    ; Limitar dentro del área virtual
    if (newX < virtual_left)
        newX := virtual_left
    if (newX >= virtual_right)
        newX := virtual_right - 1
    if (newY < virtual_top)
        newY := virtual_top
    if (newY >= virtual_bottom)
        newY := virtual_bottom - 1

    MouseMove(newX, newY, 0)
}

; Monitores: (llamalo antes de usar MoveCursor en el script principal)
; CoordMode y setup inicial acá
DllCall("user32.dll\SetThreadDpiAwarenessContext", "ptr", -3)
CoordMode("Mouse", "Screen")

monitorCount := MonitorGetCount()
monitors := []
Loop monitorCount {
    MonitorGet(A_Index, &left, &top, &right, &bottom)
    monitors.Push({ left: left, top: top, right: right, bottom: bottom })
}

virtual_left := monitors[1].left
virtual_top := monitors[1].top
virtual_right := monitors[1].right
virtual_bottom := monitors[1].bottom

for index, mon in monitors {
    if (mon.left < virtual_left)
        virtual_left := mon.left
    if (mon.top < virtual_top)
        virtual_top := mon.top
    if (mon.right > virtual_right)
        virtual_right := mon.right
    if (mon.bottom > virtual_bottom)
        virtual_bottom := mon.bottom
}
```

#### 2.3. Guardar el archivo como:  
```plaintext
MouseKeyboardControl.ahk
```

---

### 3. Ejecutar el Script

1. Abrí PowerShell o el Explorador de Archivos.  
2. Doble clic en el archivo `MouseKeyboardControl.ahk`.  
3. Verificá que el ícono verde de AutoHotkey aparezca en la bandeja del sistema.

---

## 🎹 Atajos de Teclado Disponibles

| Combinación            | Acción                                     |
|------------------------|--------------------------------------------|
| `Ctrl + Alt + H`       | Mueve la ventana al lado izquierdo (snap)  |
| `Ctrl + Alt + L`       | Mueve la ventana al lado derecho (snap)    |
| `Ctrl + Alt + K`       | Maximiza o mueve ventana hacia arriba      |
| `Ctrl + Alt + J`       | Minimiza o mueve ventana hacia abajo       |
| `Win + Shift + H`      | Mueve el mouse a la izquierda              |
| `Win + Shift + L`      | Mueve el mouse a la derecha                |
| `Win + Shift + K`      | Mueve el mouse hacia arriba                |
| `Win + Shift + J`      | Mueve el mouse hacia abajo                 |
| `Win + Shift + U`      | Clic izquierdo                             |
| `Win + Shift + O`      | Clic derecho                               |
| `Win + Shift + M`      | Scroll hacia arriba                       |
| `Win + Shift + N`      | Scroll hacia abajo                        |

---

## ⚙️ Personalización Recomendada

### Cambiar la velocidad de movimiento
```ahk
step := 30 ; Cambiar a 10 para más precisión, o 50 para velocidad turbo
```

### Ajustar el scroll
```ahk
Send("{WheelUp 3}") ; Aumentá o reducí el número de pasos (3 es normal)
```

---

## 🛠️ Ejecutar el Script al Iniciar Windows

1. Presioná `Win + R` y escribí:  
   ```plaintext
   shell:startup
   ```
2. Copiá el archivo `MouseKeyboardControl.ahk` dentro de esa carpeta.  
✅ Así se ejecutará automáticamente cada vez que inicies sesión.

---

## ✅ Solución de Problemas

| Problema                          | Solución                                                         |
|----------------------------------|------------------------------------------------------------------|
| El mouse no se mueve             | Verificar que el script esté corriendo. Usar `Win + Shift + H`.  |
| El scroll no funciona en apps    | Aumentar el valor: `Send("{WheelUp 5}")`. Probar en otra app.   |
| El puntero se pierde en el borde | Revisar código de límites (`virtual_left`, etc.) esté funcionando. |
| Los movimientos de ventana no responden | Verificar que `Ctrl + Alt + HJKL` no estén tomados por otra app. |

---

## 🧠 Siguientes Niveles (Opcional)

- Agregar movimiento en diagonales.
- Scroll horizontal (`WheelLeft`, `WheelRight`).
- Aceleración progresiva del puntero.
- Macros de productividad.

---

## ✨ Cierre

Con esta configuración no necesitás mouse para nada. Manejo total desde el teclado, tipo hacker elite o maestro Vim.  
Si querés agregar mejoras o automatizaciones, está todo listo para seguir.

---

# 🧑‍💻 Autor

**Tu nombre aquí**  
Versión: 1.0  
Fecha: 2025

