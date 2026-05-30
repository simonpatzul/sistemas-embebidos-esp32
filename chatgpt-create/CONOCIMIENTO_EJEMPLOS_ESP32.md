# Conocimiento consolidado: ejemplos ESP32 con ESP-IDF

Este archivo resume los ejemplos del repositorio `esp32-examples-espidf-main` para que el GPT tutor tenga una referencia compacta de los codigos trabajados en clase. El framework usado es ESP-IDF en C, no Arduino.

## Reglas oficiales del examen

- El codigo se entrega mediante un repositorio de GitHub creado por cada estudiante.
- Se recomienda crear el repositorio antes del examen.
- Durante el examen esta prohibido usar inteligencia artificial, redes sociales, medios de comunicacion, notas digitales, codigos anteriores digitales o paginas web.
- Solo se permite tener abierto Visual Studio Code y, al final para entrega, Git Bash.
- Las notas y ejemplos de codigo permitidos deben estar impresos o escritos a mano.
- No habra montaje fisico ni se deben llevar componentes electronicos.
- El tutor debe usarse solo antes del examen para preparar material, ejercicios y simulacros.

## Temas oficiales del final

- Teoria de sistemas embebidos.
- Manejo de GPIO: entradas y salidas digitales.
- Interrupciones y Timers.
- ADC, DAC y PWM.
- Comunicaciones: UART, I2C, SPI, BLE.
- Manejo de registros: configuraciones, lectura y escritura.
- Arquitectura de firmware por capas.

## Hipotesis de estilo del examen

El examen probablemente sera un problema general con un contexto amplio, parecido a un reto de diseno o laboratorio aplicado. El contexto puede ser biomedico, hospitalario, industrial, de monitoreo, automatizacion o instrumentacion.

La parte teorica y la parte practica pueden salir del mismo enunciado. Por eso se debe practicar leer textos largos, extraer requisitos, identificar perifericos, decidir una arquitectura de firmware y responder preguntas conceptuales asociadas al mismo sistema.

Ejemplos de contextos esperados:
- Sistema biomedico de monitoreo de paciente.
- Agitador de muestras de sangre en laboratorio clinico.
- Incubadora o sistema de control ambiental.
- Panel HMI industrial con acceso restringido.
- Sistema de alarmas hospitalarias.
- Control de motor con proteccion.

Ejemplo base de caso:
- Un laboratorio clinico necesita controlar un agitador de muestras de sangre.
- El sistema usa motor DC, potenciometro, pulsadores, LEDs y displays.
- El potenciometro se lee con ADC.
- El motor se controla con PWM.
- Los pulsadores seleccionan direccion.
- Los LEDs indican direccion.
- Los displays muestran potencia de 000 a 100.
- Debe evitarse inversion brusca de giro para proteger motor y etapa de potencia.
- Se evalua funcionamiento, arquitectura por capas, protecciones y calidad de diseno.

Preguntas derivadas de ese caso:
- Por que un motor no se conecta directo a un pin?
- Para que sirve PWM en el control de motor?
- Como se convierte una lectura ADC en porcentaje?
- Como evitar que ambos LEDs esten encendidos?
- Como proteger una carga inductiva?
- Como separar aplicacion, servicios, drivers y hardware?

## Estructura general de los ejemplos

- Lenguaje: C.
- Framework: ESP-IDF.
- Punto de entrada: `app_main()`.
- Temporizacion frecuente: `vTaskDelay(pdMS_TO_TICKS(ms))`.
- Configuracion de perifericos mediante estructuras y funciones de ESP-IDF.
- Enfoque didactico: configurar periferico, ejecutar ciclo principal, leer/escribir senales, mostrar resultados por consola o comunicacion.

## GPIO

### ESP32_GPIO_EJ1

Objetivo: encender y apagar un LED conectado al GPIO 15 con periodo de 1 segundo.

Conceptos:
- Configuracion de pin como salida con `gpio_config_t`.
- Mascara de pin con `(1UL << LED_PIN)`.
- Deshabilitar pull-up y pull-down en salida.
- Cambiar estado con `gpio_set_level()`.
- Usar `vTaskDelay(pdMS_TO_TICKS(500))` para retardos de 500 ms.

Puntos de examen:
- Diferencia entre numero de GPIO y mascara de bits.
- Por que se usa `GPIO_MODE_OUTPUT`.
- Como cambia la frecuencia si cambia el delay.

### ESP32_GPIO_EJ2

Objetivo: controlar un LED con un pulsador usando GPIO.

Conceptos:
- Un pin como entrada y otro como salida.
- Lectura de entrada digital con `gpio_get_level()`.
- Escritura de salida digital con `gpio_set_level()`.
- Pull-up/pull-down segun conexion fisica del boton.
- Rebote mecanico del pulsador como posible problema practico.

Puntos de examen:
- Interpretar boton activo en alto o activo en bajo.
- Explicar pull-up y pull-down.
- Identificar fallas si la entrada queda flotante.

### ESP32_GPIO_EJ3

Objetivo: alarma de incubadora que se activa cuando dos o mas condiciones estan presentes.

Entradas:
- A: puerta abierta.
- B: temperatura alta.
- C: humedad alta.

Salida:
- Z: alarma.

Condicion logica:
- Z se activa si al menos dos entradas son 1.
- Forma booleana esperada: `Z = AB + AC + BC`.

Conceptos:
- Tabla de verdad.
- Logica combinacional.
- Implementacion de expresiones booleanas con entradas GPIO.

Puntos de examen:
- Derivar la tabla de verdad.
- Simplificar la expresion.
- Traducir logica booleana a codigo C.

## Interrupciones y Timers

Conceptos:
- Interrupcion: evento que cambia el flujo normal del programa.
- ISR: rutina de servicio de interrupcion.
- Flanco de subida: cambio de 0 a 1.
- Flanco de bajada: cambio de 1 a 0.
- Timer: periferico usado para medir tiempo, generar eventos periodicos o temporizar tareas.

Buenas practicas:
- La ISR debe ser corta.
- La ISR puede activar una bandera y la logica principal procesa la accion.
- Evitar procesamiento pesado dentro de interrupciones.
- Considerar antirrebote en botones.
- Usar timers para muestreo periodico, control de tiempos o actualizacion de PWM.

Puntos de examen:
- Diferencia entre polling e interrupciones.
- Por que no conviene hacer trabajo pesado en una ISR.
- Como usar un timer para tomar muestras ADC periodicas.
- Como combinar GPIO, interrupcion y antirrebote.

## UART

### ESP32_UART_EJ1

Objetivo: encender y apagar un LED al recibir caracteres ASCII desde el PC por UART y responder por consola serial.

Conceptos:
- UART como comunicacion serial asincrona.
- Recepcion de caracteres.
- Comandos simples, por ejemplo letras para encender o apagar.
- Respuesta del ESP32 hacia consola.

Puntos de examen:
- Diferencia entre caracter ASCII y valor numerico.
- Parametros UART: baud rate, bits de datos, paridad, bits de parada.
- Relacion entre UART fisica y consola serial.

### ESP32_UART_EJ2

Objetivo: leer voltaje de un potenciometro con ADC y mostrarlo por UART en consola.

Conceptos:
- ADC convierte voltaje analogico a valor digital.
- El valor leido puede escalarse a voltaje.
- UART permite visualizar datos en el monitor serial.

Puntos de examen:
- Resolucion ADC y rango de voltaje.
- Diferencia entre lectura cruda y voltaje calculado.
- Muestreo periodico y visualizacion serial.

## PWM y ADC

### ESP32_PWM_ADC_EJ1

Objetivo: dimmer de LED. Variar brillo con PWM segun el voltaje de un potenciometro leido por ADC.

Conceptos:
- ADC mide posicion del potenciometro.
- PWM controla brillo mediante ciclo de trabajo.
- El duty cycle se ajusta proporcionalmente a la lectura ADC.

Puntos de examen:
- Diferencia entre frecuencia PWM y ciclo de trabajo.
- Por que PWM puede controlar brillo sin generar voltaje analogico real.
- Como mapear rango ADC a rango de duty.

## Arquitectura de firmware por capas

Capas recomendadas:
- Capa de hardware: pines, buses y perifericos fisicos.
- Capa de drivers: manejo directo de perifericos y registros.
- Capa de servicios: funciones reutilizables que combinan drivers.
- Capa de aplicacion: logica del problema.

Ejemplo:
```text
main/
  main.c
  app.c
  app.h
  sensor_driver.c
  sensor_driver.h
  board.h
```

Puntos de examen:
- Donde ubicar una funcion que lee un registro por I2C.
- Donde ubicar la logica que activa una alarma.
- Por que no conviene mezclar todo en `app_main()`.
- Como separar configuracion de hardware, driver y aplicacion.

## DAC

### ESP32_DAC_EJ1

Objetivo: generar una senal sinusoidal de 200 Hz usando el DAC interno del ESP32.

Conceptos:
- DAC convierte valores digitales en niveles analogicos.
- Una tabla de muestras puede aproximar una onda senoidal.
- La frecuencia depende de numero de muestras y tiempo entre muestras.

Puntos de examen:
- Relacion entre frecuencia de salida, frecuencia de muestreo y cantidad de muestras.
- Diferencia entre DAC y PWM filtrado.

### ESP32_DAC_EJ2

Objetivo: generar dos valores especificos de voltaje con el DAC en modo one-shot.

Conceptos:
- Salida analogica fija.
- Conversion entre valor digital DAC y voltaje aproximado.

Puntos de examen:
- Resolucion del DAC.
- Limitaciones de precision y rango de salida.

### ESP32_DAC_EJ3

Objetivo: generar una senal coseno usando la funcion especial del DAC del ESP32.

Conceptos:
- Generacion por hardware de onda coseno.
- Configuracion de frecuencia/amplitud segun capacidades del periferico.

Puntos de examen:
- Ventaja de usar generacion por hardware frente a generar muestras por software.

## I2C

### ESP32_I2C_EJ1

Objetivo:
1. Configurar fecha y hora en un RTC DS1307 escribiendo registros internos por I2C.
2. Leer fecha y hora cada segundo y mostrarla por monitor serial.

Conceptos:
- I2C usa lineas SDA y SCL.
- Tiene direccionamiento de dispositivos.
- El DS1307 se controla mediante registros.
- La fecha/hora se maneja en BCD.
- Escritura: direccion de dispositivo, direccion de registro, datos.
- Lectura: seleccionar registro y leer bytes.

Puntos de examen:
- Diferencia entre direccion de dispositivo y direccion de registro.
- Por que se usa BCD en RTC.
- Secuencia basica de lectura/escritura I2C.
- Papel de resistencias pull-up en SDA/SCL.

## SPI

### ESP32_SPI_EJ1

Objetivo: mostrar numeros en un display de 7 segmentos de 4 digitos usando MAX7219 por SPI.

Conceptos:
- SPI usa SCLK, MOSI, MISO y CS/SS.
- MAX7219 se configura escribiendo registros internos.
- Para displays, se envian pares direccion-registro y dato.

Puntos de examen:
- Diferencia entre SPI e I2C.
- Rol de chip select.
- Por que SPI suele ser full-duplex aunque algunos dispositivos solo usen escritura.
- Configuracion de registros del MAX7219.

## Bluetooth Classic SPP

### ESP32_BT_SPP

Objetivo: comunicacion serial inalambrica con Bluetooth Classic usando perfil SPP.

Stack:
- Bluedroid.

Perfil:
- SPP, Serial Port Profile.

Comportamiento:
- El ESP32 actua como puerto serial Bluetooth.
- Al recibir `E`, enciende LED en GPIO26 y responde `LED encendido`.
- Al recibir `A`, apaga LED y responde `LED apagado`.
- Otros comandos producen `Comando desconocido`.

Conceptos:
- Bluetooth Classic BR/EDR.
- RFCOMM emula comunicacion serial.
- En PC puede aparecer como puerto COM virtual.
- En Android se pueden usar apps SPP.
- iPhone/iPad normalmente no permiten acceso a SPP clasico.

Puntos de examen:
- Diferencia entre Bluetooth Classic y BLE.
- Que problema resuelve SPP.
- Limitaciones de compatibilidad con iOS.

## Bluetooth Low Energy BLE NUS

### ESP32_BLE_NUS

Objetivo: comunicacion tipo UART sobre BLE usando Nordic UART Service.

Stack:
- NimBLE.

Servicio:
- Nordic UART Service, NUS.

UUIDs:
- Servicio NUS: `6E400001-B5A3-F393-E0A9-E50E24DCCA9E`
- Caracteristica RX: `6E400002-B5A3-F393-E0A9-E50E24DCCA9E`
- Caracteristica TX: `6E400003-B5A3-F393-E0A9-E50E24DCCA9E`

Comportamiento:
- RX recibe escrituras desde el dispositivo central hacia el ESP32.
- TX envia notificaciones desde el ESP32 al dispositivo central.
- Al recibir `E`, enciende LED en GPIO2 y notifica `LED encendido`.
- Al recibir `A`, apaga LED y notifica `LED apagado`.
- Otros comandos producen `Error: comando desconocido`.

Conceptos:
- BLE se basa en GATT, servicios y caracteristicas.
- NUS emula comunicacion serial sobre BLE.
- Compatible con herramientas como nRF Connect.
- BLE es preferible para compatibilidad moderna, incluyendo iOS, frente a SPP clasico.

Puntos de examen:
- Diferencia entre servicio y caracteristica GATT.
- Diferencia entre escritura y notificacion.
- Por que se usa NUS para comunicacion tipo UART en BLE.
- Comparacion entre SPP y BLE NUS.

## Comparaciones clave para examen

| Tema | Idea central | Pregunta tipica |
| --- | --- | --- |
| GPIO | Entrada/salida digital | Como configurar un pin y evitar entradas flotantes |
| UART | Serial asincrona punto a punto | Que parametros deben coincidir entre transmisor y receptor |
| I2C | Bus con direcciones y registros | Como leer un registro de un RTC |
| SPI | Bus sincronico con chip select | Como seleccionar un periferico y enviar registros |
| ADC | Analogico a digital | Como convertir lectura cruda a voltaje |
| DAC | Digital a analogico | Como generar voltajes o formas de onda |
| PWM | Onda digital con duty variable | Como controlar brillo o potencia promedio |
| BLE | GATT, servicios y caracteristicas | Como se implementa comunicacion tipo UART con NUS |
| SPP | Serial sobre Bluetooth Classic | Cuando sirve y que limitaciones tiene |

## Preguntas de practica sugeridas

1. Explique la diferencia entre configurar un GPIO como entrada con pull-up y como entrada flotante.
2. Si un LED parpadea con 500 ms encendido y 500 ms apagado, cual es su frecuencia?
3. Derive la expresion booleana de una alarma que se activa cuando dos de tres sensores estan activos.
4. Compare UART, SPI e I2C en numero de lineas, direccionamiento y usos tipicos.
5. Explique la secuencia para leer la hora desde un DS1307 por I2C.
6. Como se calcula el voltaje a partir de una lectura ADC?
7. Cual es la diferencia entre PWM y DAC?
8. Por que BLE NUS puede ser mas conveniente que Bluetooth Classic SPP?
9. Que significa que una caracteristica BLE sea RX o TX desde el punto de vista del ESP32?
10. Que errores comunes pueden aparecer al conectar botones, buses I2C o displays SPI?
