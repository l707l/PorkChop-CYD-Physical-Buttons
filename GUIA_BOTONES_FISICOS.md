# 🎮 Guía de Botones Físicos para Porkchop en CYD (ESP32-2432S028R)

Esta versión incluye soporte nativo para **botones físicos**, diseñada especialmente para placas **CYD (ESP32-2432S028R)** que tengan el **panel táctil dañado, roto o no calibrado**.

---

## 🔌 Diagrama de Conexiones de Botones

La placa CYD cuenta con conectores blancos JST-GH / JST-SH (1.25 mm) en sus laterales y un botón físico ya soldado (`BOOT`). Todos los botones se conectan en configuración **activa a nivel bajo (Active LOW)**: un polo del pulsador va al pin GPIO y el otro polo va a **GND**.

```
                           CYD (ESP32-2432S028R)
                     +-------------------------------+
                     |                               |
                     |         PANTALLA LCD          |
                     |            2.8"               |
                     |          ILI9341              |
                     |                               |
                     +-------------------------------+
                      [P3]           [CN1]      [BOOT]
                     (IO22)          (IO3)      (IO0)
                       |               |          |
                   [Pulsador]      [Pulsador]  [Botón]
                    ARRIBA           ABAJO     OK/SALIR
                       |               |       (Soldado)
                      GND             GND
```

### Tabla de Pines Asignados:

| Botón | Función en Porkchop | Pin CYD | Conector en Placa | Conexión |
| :---: | :--- | :---: | :--- | :--- |
| **ARRIBA** | Desplazarse arriba / Modo anterior | **GPIO 22** | **Conector P3** (Pin 4) | Pulsador entre GPIO 22 y GND |
| **ABAJO** | Desplazarse abajo / Modo siguiente | **GPIO 27** *(o GPIO 3)* | **Conector P1** *(o CN1 Pin 4)* | Pulsador entre GPIO 27/3 y GND |
| **OK / SELECT** | Entrar al modo / Confirmar | **GPIO 0** | **Botón BOOT** de la placa | **¡Ya viene soldado en la placa!** |
| **SALIR / ATRÁS** | Salir al menú principal (Botón `[X]`) | **GPIO 0 (Largo)** | Mantener presionado `BOOT` > 700 ms | **¡Sin botones extra!** |

> [!TIP]
> **No necesitas resistencias externas:**
> Los pines `GPIO 0`, `GPIO 22` y `GPIO 27` (o `GPIO 3`) utilizan las resistencias *pull-up* internas del ESP32 (`INPUT_PULLUP`), por lo que solo requieres conectar los pulsadores directo a tierra (`GND`).

---

## 🕹️ Cómo Navegar en Porkchop sin Touch

1. **En la Pantalla de Inicio (IDLE):**
   * **Pulsar OK (`BOOT / GPIO 0`):** Abre el menú principal (`MENU`).
   * **Mantener presionado OK (> 700 ms):** Cambia el tema de color visual.

2. **En el Menú de Selección de Modos:**
   * **Pulsar ARRIBA (`GPIO 22`):** Sube un elemento en la lista.
   * **Pulsar ABAJO (`GPIO 27` o `3`):** Baja un elemento en la lista.
   * **Pulsar OK (`BOOT / GPIO 0`):** Entra al modo seleccionado (OINK, DNH, WARHOG, BACON, SPECTRUM, SETTINGS, etc.).
   * **Mantener presionado OK (> 700 ms):** Vuelve al estado IDLE.

3. **Dentro de cualquier Modo Activo:**
   * **Mantener presionado OK (> 700 ms):** Activa automáticamente el botón virtual `[X]` de la barra inferior, deteniendo el ataque o escaneo y regresando al menú.

---

## 📱 Control Remoto Web Alternativo (Vía Wi-Fi)

Si prefieres no conectar pulsadores físicos:
1. Conéctate con tu teléfono o PC a la red Wi-Fi que genera Porkchop (o a la misma red local si está configurado en modo cliente).
2. Abre tu navegador web y entra a:
   `http://192.168.4.1`
3. Tendrás una consola táctil completa con botones:
   - `L` (Izquierda / Arriba)
   - `MID` (Centro / Seleccionar)
   - `R` (Derecha / Abajo)
   - `HOLD` (Pulsación sostenida)
   - Acceso directo a todos los modos: OINK, DNH, HOG, BCN, MENU, STATS, etc.

---

## 🛠️ Cómo Compilar y Flashear este Firmware

### Requisitos en Arduino IDE:
1. Placa: **ESP32 Dev Module**
   - Partition Scheme: `Huge APP (3MB No OTA/1MB SPIFFS)` o `Minimal SPIFFS (Large APPS with OTA)`
   - Upload Speed: `921600`
2. Librería: `TFT_eSPI` (v2.5.0+)
   - Recuerda copiar el archivo `User_Setup.h` incluido en este repositorio dentro de `Documents/Arduino/libraries/TFT_eSPI/User_Setup.h`.

### Flasheo rápido por línea de comandos (esptool):
```bash
esptool.py --chip esp32 --port COM11 --baud 921600 write-flash 0x0 build/Compiled\ Binary/PorkChop_CYD.ino.merged.bin
```
