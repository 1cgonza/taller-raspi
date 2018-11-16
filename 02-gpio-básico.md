# GPIO Básico

## Pinout

![](./img/pi3_gpio.png)

Una aplicación muy útil para investigar más a fondo cada pin: [https://pinout.xyz/](https://pinout.xyz/)

## Control de GPIOs con Python y GPIO Zero

Raspbian ya tiene instalado Python 3 y la librería GPIO Zero. Esto es suficiente para controlar componentes con los pines GPIO de la Raspberry.

[Documentación de GPIO Zero](https://gpiozero.readthedocs.io/en/stable/)

### LED

Materiales:
- LED
- Resistencia superior a 50 ohms

![](/img/led.png)

``` python
from gpiozero import LED
from time import sleep

led = LED(14)

while True:
  led.on()
  sleep(.3)
  led.off()
  sleep(.3)
```

### LED v2 - software PWM
``` python
from gpiozero import PWMLED
from time import sleep

led = PWMLED(14)
led.value = .5
```

### LED v3 - software PWM dim
``` python
from gpiozero import PWMLED
from time import sleep

led = PWMLED(14)

i = 0

while i <= 1:
  led.value = i
  i += .05
  sleep(.1)

i = 1

while i >= 0:
  led.value = i
  i -= .05
  sleep(.1)

led.value = 0
```


### Botón

Materiales:
- LED
- Resistencia superior a 50 ohms
- Botón

![](/img/button.png)

``` python
from gpiozero import LED, Button
from time import sleep

led = LED(14)
button = Button(2)

while True:
  button.wait_for_press()
  led.toggle()
  sleep(.5)
```

#### botón v2 - prender LED sólo cuando este presionado

```python
from gpiozero import LED, Button

led = LED(14)
button = Button(2)

# Estos eventos no bloquean el programa
# Tener en cuenta que no llamamos las funciones .on y .off,
# solo se declaran para que el evento las ejecute cuando sea necesario
button.when_pressed = led.on
button.when_released = led.off
```

### Sensores

A pesar de que las entradas de la Pi son exclusivamente digitales, la librería gpiozero tiene interfaces para controlar algunos sensores sin necesidad de componentes adicionales. 

[API de entradas con gpiozero](https://gpiozero.readthedocs.io/en/stable/api_input.html)

#### Fotocelda con condensador

[API](https://gpiozero.readthedocs.io/en/stable/api_input.html#light-sensor-ldr)

Materiales:
- LED
- Resistencia superior a 50 ohms
- Condensador 1uf

![](/img/fotocelda_condensador.png)

``` python
from gpiozero LightSensor, PWMLED

# La clase LightSensor asume que se usa un condensador de 1uf,
# 
celda = LightSensor(4)
led = PWMLED(14)

while True:
  led.value = celda.value
```

### Salidas análogas con MCP3008

El MCP3008 es un conversor analógico digital (ADC) con 8 canales de entrada analógica y se comunica con la Pi por SPI. Primero debemos configurar la interfaz SPI de la Pi.

En el terminal usamos el siguiente comando para instalas `spidev`:

``` bash
sudo apt-get install python3-spidev python-spidev
```

En **Preferencias -> Configuración de Raspberry Pi** activamos la interfaz **SPI**

Luego le damos aceptar y toca reiniciar la Pi.

### Potenciómetro

Materiales:
- LED
- Resistencia superior a 50 ohms
- MCP3008
- Potenciómetro

![](/img/pot_MCP3008.png)

``` python
from gpiozero import MCP3008, PWMLED

pot = MCP3008(0)
led = PWMLED(21)

while True:
  led.value = pot.value
```

