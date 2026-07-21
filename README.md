# Pastillero-digital
Para nuestro proyecto final de electrónica digital I realizamos el diseño e implementación de un pastillero digital basado en FPGA, con el objetivo de facilitar el cumplimiento de la toma oportuna de medicamentos, especialmente para personas mayores que tienen dificultades de recordar los múltiples horarios.  

### Estructura de proyecto

- `pastillero_top.vhd` —  Su función es interconectar todos los módulos del sistema, coordinar el flujo de información entre ellos y controlar el funcionamiento completo del pastillero digital.
- `rtc_ds3231.vhd` — Arc
- `LIB_LCD_INTESC.vhd` — Arc
- `PROCESADOR_LCD.vhd` — Arc
- `COMANDOS_LCD.vhd` — Arc
- `CARACTERES_ESPECIALES.vhd` — Arc
