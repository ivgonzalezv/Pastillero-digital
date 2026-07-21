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
  
Funcionamiento:
Una vez que la FPGA establece la condición de inicio (START) del protocolo I²C, comienza la comunicación enviando la dirección del módulo RTC DS3231 junto con el bit de escritura. Después de que el DS3231 confirma la recepción de la dirección del dispositivo y del registro mediante las señales de reconocimiento (ACK), la FPGA genera una condición de inicio repetido (Repeated START). Esta condición permite mantener el control del bus I²C sin liberar la comunicación y cambiar inmediatamente de una operación de escritura a una operación de lectura.

Posteriormente la FPGA vuelve a transmitir la dirección del DS3231, pero esta vez con el bit de lectura. A partir de ese momento, el RTC comienza a transmitir de manera secuencial el contenido de sus registros internos, después de leer el registro de segundos el dispositivo continúa enviando automáticamente los registros de minutos, horas y día de la semana, sin que sea necesario especificar nuevamente la dirección de cada uno de ellos. Durante este proceso, la FPGA confirma la correcta recepción de cada dato mediante señales ACK, indicando al RTC que continúe con la transmisión hasta obtener toda la información requerida.

Finalmente, una vez recibidos los valores de segundos, minutos, horas y día de la semana, la FPGA envía una condición NACK para indicar que no se requieren más datos y posteriormente genera la condición de STOP, dando por terminada la comunicación.
- `sec_out`  — Salida - Segundos leídos del DS3231.

### Módulo `pastillero_top.vhd`
El módulo posee las siguientes señales de entrada y salida:
- `clk` — Entrada - Reloj principal de la FPGA de 50 MHz.
- `btn_config` — Entrada- Botón físico el cuál permite desactivar la alarma, pasa por un filtro inteligente que bloquea su lectura durante los primeros 30 segundos de cualquier alarma activa, evitando que el usuario cambie la hora accidentalmente mientras suena la alerta.
- `RTC_SDA` — Entrada - Línea de datos bidireccional de la interfaz I2C del RTC DS3231.
- `RTC_SCL` — Salida - Línea de reloj de la interfaz I2C para el RTC DS3231.
- `alarm_active / buzzer_out`  — Salida - Señal lógica de activación de la alarma sonora/visual, se activa en '1' lógico cuando el tiempo del RTC coincide con cualquiera de las tres alarmas programadas. Controla el buzzer o el indicador de alerta durante un intervalo de tiempo determinado.
- `LCD_RS` — Salida - Indica a la pantalla si el dato que se está enviando es un comando de configuración ('0') o un carácter a imprimir ('1').
- `LCD_RW` — Salida - Se mantiene conectado internamente a '0' (GND) para forzar a la pantalla a operar en modo exclusivo de escritura.
- `LCD_E` — Salida - Envía el pulso de sincronización para que el controlador HD44780 de la pantalla lea los datos del bus.
- `LCD_DATA` — Salida - Bus paralelo de datos de 8 bits. Transporta los códigos ASCII e instrucciones que se envían directamente a la pantalla LCD para dibujar la hora, el día y los nombres de los medicamentos

El módulo pastillero_top actúa como la entidad central del sistema. Su función principal es integrar, coordinar y sincronizar los diferentes subsistemas que componen el pastillero automatizado: el reloj en tiempo real (RTC DS3231), la lógica de comparación de alarmas, el filtro de protección para la configuración y la interfaz gráfica basada en una pantalla LCD 16x2.

Funciones:
- Lectura y Comunicación I2C con el RTC: El módulo se encarga de gestionar la comunicación serie bidireccional sobre el bus I2C utilizando las líneas RTC_SDA y RTC_SCL. Mediante esta interfaz, se extraen constantemente los registros de tiempo transmitidos por el RTC en formato BCD, correspondientes a los segundos, minutos, horas y día de la semana.
- Bloque de Comparación de Alarmas Múltiples: El sistema gestiona tres alarmas independientes configuradas internamente mediante constantes predefinidas. El módulo realiza una comparación lógica en paralelo entre la hora actual leída del RTC y las horas/minutos programados.
- Filtro Inteligente de Botones y Anti-Bloqueo: Para garantizar la estabilidad del sistema y evitar errores de manipulación por parte del usuario, se implementa un proceso de filtrado síncrono sobre la entrada btn_config. Esto impide que el usuario active accidentalmente el modo de configuración del RTC mientras suena la alarma o cuando el sistema se encuentra en un ciclo de alerta, evitando desconfigurar la hora por error.
- Control Dinámico del Estado de Alarma y Buzzer: El módulo supervisa la señal alarm_active para controlar la salida física hacia la señal sonora. Al activarse la alarma, el sistema mantiene la alerta activa durante un intervalo temporal definido sin reingresar en bucles infinitos de disparo, centralizando la gestión del estado para prevenir problemas de múltiples controladores de señal.
- Interconexión y Mapeo con la Librería Controladora de la LCD: Finalmente, el pastillero_top realiza la instanciación de la entidad controladora de la pantalla (LIB_LCD_INTESC). Le suministra las señales estabilizadas de tiempo, el día de la semana y, de forma directa, las señales lógicas de activación (ALARM1_ON, ALARM2_ON, ALARM3_ON).
Gracias a este mapeo directo, el módulo principal delega la generación física de los comandos LCD, logrando que la pantalla cambie dinámicamente entre el modo de espera (mostrando la hora y el nombre completo del día) y el modo de alarma (desplegando el nombre exacto del medicamento programado para ese horario).
