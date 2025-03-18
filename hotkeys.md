
⬅️ [Volver al Menú Principal](README.md)



# 🖱️ Manual de Configuración de Mouse, Scroll, Movimiento de Ventanas y Scroll Diagonal por Teclado con AutoHotkey v2.0 (Chocolatey Edition)
**Versión: 2.0**  
**Autor:** Jorge  
**Fecha:** 2025-03-16

---

## ✅ ¿Qué hace este sistema?
Permite mover el cursor del mouse, hacer clics, hacer scroll vertical y diagonal, y mover ventanas a los snaps de Windows **usando solo el teclado**, sin necesidad de un mouse físico.  
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
choco install autohotkey
```
✅ Esto instalará la versión correcta de AutoHotkey v2.0.

---

Abrí **Windows + r** y ejecutá:
```powershell
   shell:startup
```


✅ Esto instalará la versión correcta de AutoHotkey v2.0.

---

### 2. Crear el Script de Control de Mouse, Scroll, Movimiento de Ventanas y Scroll Diagonal

#### 2.1. Abrir **Notepad** o tu editor favorito. y guardar el crhivo con extension mi-config.ahk


#### 2.2. Copiar el siguiente código y pegarlo en el editor:

```ahk
#Requires AutoHotkey v2.0
#SingleInstance Force

; ===========================
; Mover Ventanas Snap Areas
; ===========================
^!h:: Send("#{Left}")
^!l:: Send("#{Right}")
^!k:: Send("#{Up}")
^!j:: Send("#{Down}")

; ===========================
; Configuración General
; ===========================
baseStep := 10
maxStep := 100
acceleration := 5
delay := 50

; ===========================
; Movimiento del Mouse (Con Aceleración)
; ===========================
#+h:: MoveContinuous("left", "h")
#+l:: MoveContinuous("right", "l")
#+k:: MoveContinuous("up", "k")
#+j:: MoveContinuous("down", "j")

; ===========================
; Clicks del Mouse
; ===========================
#+u:: Click("left")
#+o:: Click("right")

; ===========================
; Scroll Vertical Clásico
; ===========================
#+m:: Send("{WheelUp 3}")
#+n:: Send("{WheelDown 3}")

; ===========================
; Scroll Diagonal (Win + Alt + WASD)
; ===========================
#!w:: ScrollDiagonal("up", "right")
#!a:: ScrollDiagonal("down", "left")
#!s:: ScrollDiagonal("down", "right")
#!d:: ScrollDiagonal("up", "left")

; ===========================
; Función de Movimiento Continuo con Aceleración
; ===========================
MoveContinuous(direction, keyName) {
    global baseStep, maxStep, acceleration, delay
    step := baseStep

    while GetKeyState(keyName, "P") && (GetKeyState("LWin", "P") || GetKeyState("RWin", "P")) && (GetKeyState("LShift", "P") || GetKeyState("RShift", "P")) {
        MoveCursor(direction, step)
        step := (step + acceleration > maxStep) ? maxStep : step + acceleration
        Sleep delay
    }
}

; ===========================
; Función de Movimiento de Cursor en Pantallas Múltiples
; ===========================
MoveCursor(direction, step) {
    global monitors, virtual_left, virtual_top, virtual_right, virtual_bottom

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

; ===========================
; Scroll Diagonal (Arriba/Abajo + Izquierda/Derecha)
; ===========================
ScrollDiagonal(vertical, horizontal) {
    if (vertical = "up")
        Send("{WheelUp 3}")
    else if (vertical = "down")
        Send("{WheelDown 3}")

    MouseGetPos(&curX, &curY)

    offset := 10
    if (horizontal = "left")
        MouseMove(curX - offset, curY, 0)
    else if (horizontal = "right")
        MouseMove(curX + offset, curY, 0)
}

; ===========================
; Setup de Monitores y DPI Awareness
; ===========================
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

| Combinación              | Acción                                 |
|--------------------------|----------------------------------------|
| `Ctrl + Alt + H`         | Snap a la izquierda (Win + Left)       |
| `Ctrl + Alt + L`         | Snap a la derecha (Win + Right)        |
| `Ctrl + Alt + K`         | Maximiza ventana (Win + Up)            |
| `Ctrl + Alt + J`         | Restaura/minimiza ventana (Win + Down) |
| `Win + Shift + HJKL`     | Mueve el mouse en cualquier dirección  |
| `Win + Shift + U`        | Click izquierdo                       |
| `Win + Shift + O`        | Click derecho                         |
| `Win + Shift + M`        | Scroll hacia arriba                  |
| `Win + Shift + N`        | Scroll hacia abajo                   |
| `Win + Alt + W`          | Scroll diagonal arriba + derecha     |
| `Win + Alt + A`          | Scroll diagonal abajo + izquierda    |
| `Win + Alt + S`          | Scroll diagonal abajo + derecha      |
| `Win + Alt + D`          | Scroll diagonal arriba + izquierda   |

---

## ⚙️ Personalización Recomendada

### Cambiar la velocidad de movimiento
```ahk
baseStep := 10
maxStep := 100
acceleration := 5
delay := 50
```

### Ajustar el scroll diagonal
```ahk
offset := 10 ; desplazamiento lateral para scroll diagonal
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

- Agregar movimiento en diagonales para el cursor.
- Scroll horizontal (`WheelLeft`, `WheelRight`).
- Aceleración progresiva del scroll.
- Macros de productividad.

---

## ✨ Cierre

Con esta configuración no necesitás mouse para nada. Manejo total desde el teclado, tipo hacker elite o maestro Vim.  
Si querés agregar mejoras o automatizaciones, está todo listo para seguir.

---

# 🧑‍💻 Autor

**Jorge**  
Versión: 2.0  
Fecha: 2025-03-16

⬅️ [Volver al Menú Principal](README.md)

