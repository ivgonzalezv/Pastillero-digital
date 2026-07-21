# Pastillero-digital
Para nuestro proyecto final de electrónica digital I realizamos el diseño e implementación de un pastillero digital basado en FPGA, con el objetivo de facilitar el cumplimiento de la toma oportuna de medicamentos, especialmente para personas mayores que tienen dificultades de recordar los múltiples horarios.  

### Estructura de proyecto

- `pastillero_top.vhd` —  Su función es interconectar todos los módulos del sistema, coordinar el flujo de información entre ellos y controlar el funcionamiento completo del pastillero digital.
- `rtc_ds3231.vhd` — Este módulo es el encargado de establecer la comunicación entre la FPGA y el reloj de tiempo real DS3231 mediante el protocolo I²C.
- `LIB_LCD_INTESC.vhd` — Arc
- `PROCESADOR_LCD.vhd` — Arc
- `COMANDOS_LCD.vhd` — Arc
- `CARACTERES_ESPECIALES.vhd` — Este módulo define los patrones de los caracteres personalizados que pueden almacenarse en la CGRAM de la LCD.

### Módulo `rtc_ds3231.vhd`
El módulo posee las siguientes señales de entrada y salida.
- `clk_50m` — Entrada - Reloj principal de la FPGA de 50 MHz.
- `sda` — Entrada/Salida - Línea de datos del protocolo I²C.
- `scl` — Arc
- `COMANDOS_LCD.vhd` — Arc
- `CARACTERES_ESPECIALES.vhd`
