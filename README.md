# Laboratorio de Computación Física con Raspberry Pi

## Tabla de contenidos

- [Instalación](01-instalación.md)
- [GPIO Básico](02-gpio-básico.md)
- [Conexión remota - usar computador como monitor, teclado y mouse de la Pi](03-conexión-remota.md)
- [Referentes](referentes.md)
- [Link a clase de Computación Física de la Javeriana](https://cf.juancgonzalez.com)
- [Link a tutorial de Arduino con Processing](https://cf.juancgonzalez.com/cronograma/semana8-codigo.html)


## Camara

```python
from picamera import PiCamera
from time import sleep

camera = PiCamera()

camera.start_preview()
```

```python
import numpy as np
import cv2

captura = cv2.VideoCapture(0)
width = captura.get(cv2.CAP_PROP_FRAME_WIDTH)
height = captura.get(cv2.CAP_PROP_FRAME_HEIGHT)

grid = np.zeros((int(height), int(width), 3), np.uint8)

gridCols = 2
gridRows = 2
cellW = int(width / gridCols)
cellH = int(height / gridRows)
steps = 2
gridTotalPixels = (cellW * cellH) / steps

def gridColor(x1, x2, y1, y2, img):
  colorSum = 0

  for y in range(x1, x2, steps):
    for x in range(y1, y2, steps):
      colorSum += img[x, y]
  
  return colorSum / gridTotalPixels

while captura.isOpened():
  ret, frame = captura.read()

  gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
  gray = cv2.flip(gray, 1)

  for row in range(0, gridRows):
    for col in range(0, gridCols):
      x1 = col * cellW
      x2 = x1 + cellW
      y1 = row * cellH
      y2 = y1 + cellH
      c = gridColor(x1, x2, y1, y2, gray)
      cv2.rectangle(grid, (x1, y1), (x2, y2), (c, c, c), cv2.FILLED)

  cv2.imshow('image', grid)

  key = cv2.waitKey(20)
  if key == 27:  # esc
    break

captura.release()
cv2.destroyAllWindows

```