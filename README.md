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
El módulo posee las siguientes señales de entrada y salida:
- `clk_50m` — Entrada - Reloj principal de la FPGA de 50 MHz.
- `sda` — Entrada/Salida - Línea de datos del protocolo I²C.
- `scl` — Salida - Línea de reloj del protocolo I²C.
- `set_time_btn` — Entrada - Botón utilizado para escribir una nueva hora en el RTC.
- `sec_out`  — Salida - Segundos leídos del DS3231.
- `min_out` — Salida - Minutos leídos del DS3231.
- `hour_out` — Salida - Horas leídas del DS3231.
- `day_out` — Salida - Día de la semana leído del DS3231.
  
##Funcionamiento: Una vez que la FPGA establece la condición de inicio (START) del protocolo I²C, comienza la comunicación enviando la dirección del módulo RTC DS3231 junto con el bit de escritura. Después de que el DS3231 confirma la recepción de la dirección del dispositivo y del registro mediante las señales de reconocimiento (ACK), la FPGA genera una condición de inicio repetido (Repeated START). Esta condición permite mantener el control del bus I²C sin liberar la comunicación y cambiar inmediatamente de una operación de escritura a una operación de lectura.

Posteriormente la FPGA vuelve a transmitir la dirección del DS3231, pero esta vez con el bit de lectura. A partir de ese momento, el RTC comienza a transmitir de manera secuencial el contenido de sus registros internos, después de leer el registro de segundos el dispositivo continúa enviando automáticamente los registros de minutos, horas y día de la semana, sin que sea necesario especificar nuevamente la dirección de cada uno de ellos. Durante este proceso, la FPGA confirma la correcta recepción de cada dato mediante señales ACK, indicando al RTC que continúe con la transmisión hasta obtener toda la información requerida.

Finalmente, una vez recibidos los valores de segundos, minutos, horas y día de la semana, la FPGA envía una condición NACK para indicar que no se requieren más datos y posteriormente genera la condición de STOP, dando por terminada la comunicación.
- `sec_out`  — Salida - Segundos leídos del DS3231.
- 
