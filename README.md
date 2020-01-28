## Projecten

welkom op mijn projecten pagina van ICT in de wolken.

### Microbit
Lucien en ik hebben het project Microbit gekozen om in blok 2 te maken.
Ons doel van dit project was om een zelf rijdende robot te maken die om obstakels heen kon manoeuvreren. 
Het eerste wat we hadden gedaan is een beetje met de robot spelen en kleine codes schrijven om te kijken wat dit met de robot zou doen.
daarna hebben we een code geschreven die er voor zorgt dat de lampjes van de robot rood zijn als je je hand voor de lichtsensor houd en groen zijn als er niks voor zit.

dit is de code die we daarbij hebben geschreven:
```
from microbit import *
import neopixel

np = neopixel.NeoPixel(pin13, 12)

leftLineSensor = pin11
rightLineSensor = pin5

lightSensor = pin2
sensorSelect = pin16

def leftLights(Red, Green, Blue):
    for pixel_id in range(0, 6):
        np[pixel_id] = (Red, Green, Blue)
    np.show()

def rightLights(Red, Green, Blue):
    for pixel_id in range(6, 12):
        np[pixel_id] = (Red, Green, Blue)
    np.show()

def lineDetector(side):  # 0 == left, 1 == right
    if(side == 0):
        isLine = leftLineSensor.read_digital()
    else:
        isLine = rightLineSensor.read_digital()

    if(isLine == 1):  # Sensor can see the line
        return True
    else:
        return False
        
        
while True:
    isLeftLight = lineDetector(0)
    isRightLight = lineDetector(1)
    
    if(isLeftLight is True) and (isRightLight is False):
        leftLights(0,255,0)
        rightLights(255,0,0)

    elif(isRightLight is True) and (isLeftLight is False):
        rightLights(0,255,0)
        leftLights(255,0,0)

    elif(isRightLight is False) and (isLeftLight is False):
        rightLights(255,0,0)
        leftLights(255,0,0)
        
    else:
        leftLights(0,255,0)
        rightLights(0,255,0)
   ```
   
Nadat wij deze code succesvol hebben kunnen uitvoeren zijn we begonnen aan het maken van de code waardoor de robot zelfstandig zich door verschillende omgevingen kon manoeuvreren. Bij deze code hebben we gebruik gemaakt van een 
