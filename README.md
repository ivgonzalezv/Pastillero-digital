# Pastillero-digital
Para nuestro proyecto final de electrónica digital I realizamos el diseño e implementación de un pastillero digital basado en FPGA, con el objetivo de facilitar el cumplimiento de la toma oportuna de medicamentos, especialmente para personas mayores que tienen dificultades de recordar los múltiples horarios.  

### Estructura de proyecto

- `pastillero_top.vhd` —  Su función es interconectar todos los módulos del sistema, coordinar el flujo de información entre ellos y controlar el funcionamiento completo del pastillero digital.
- `rtc_ds3231.vhd` — Este módulo es el encargado de establecer la comunicación entre la FPGA y el reloj de tiempo real DS3231 mediante el protocolo I²C.
- `LIB_LCD_INTESC.vhd` — Este módulo genera una memoria de instrucciones (INST) dependiendo de lo que se quiera mostrar.
- `PROCESADOR_LCD.vhd` — Este módulo se encarga de convertir esas instrucciones en las señales eléctricas (RS, RW, ENA y DATA) que entiende el controlador HD44780 de la LCD.
- `COMANDOS_LCD.vhd` — Traduce instrucciones de alto nivel en códigos de 9 bits que el procesador puede interpretar.
- `CARACTERES_ESPECIALES.vhd` — Este módulo define los patrones de los caracteres personalizados que pueden almacenarse en la CGRAM de la LCD.

### Módulo `rtc_ds3231.vhd`
El módulo posee las siguientes señales de entrada y salida.
- `clk_50m` — Entrada - Reloj principal de la FPGA de 50 MHz.
- `sda` — Entrada/Salida - Línea de datos del protocolo I²C.
- `scl` — Salida - Línea de reloj del protocolo I²C.
- `set_time_btn` — Entrada - Botón utilizado para escribir una nueva hora en el RTC.
- `sec_out`  — Salida - Segundos leídos del DS3231.
- `min_out` — Salida - Minutos leídos del DS3231.
- `hour_out` — Salida - Horas leídas del DS3231.
- `day_out` — Salida - Día de la semana leído del DS3231.


- `sec_out`  — Salida - Segundos leídos del DS3231.
- 
