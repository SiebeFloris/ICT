## Projecten

welkom op mijn projecten pagina van ICT in de wolken.
in deze projecten hebben we gebruik gemaakt van de volgende websites:
[C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/)
[NEAT](https://neat-python.readthedocs.io/en/latest/)

### Microbit

Dit project heb ik samen gemaakt met: [Lucien Kerssens](https://lucienpemberton.github.io/ICT/)

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
   
Nadat wij deze code succesvol hebben kunnen uitvoeren zijn we begonnen aan het maken van de code waardoor de robot zelfstandig een lijn kon volgen. Bij deze code hebben we gebruik gemaakt van de lichtsensoren aan de onderkant van de robot.

Dit is de code die we daarvoor hebben geschreven:
```
from microbit import *
import neopixel  # Neopixel Library so we can control the NeoPixels lights

np = neopixel.NeoPixel(pin13, 12)

leftSpeed = pin0
leftDirection = pin8
rightSpeed = pin1
rightDirection = pin12

leftLineSensor = pin11
rightLineSensor = pin5

lightSensor = pin2
sensorSelect = pin16


def leftLights(Red, Green, Blue):
    for pixel_id in range(0, 6):  # Start of for loop
        np[pixel_id] = (Red, Green, Blue)  # Code to be executed in the loop
    np.show() # Change the NeoPixels colour


def rightLights(Red, Green, Blue):
    for pixel_id in range(6, 12):
        np[pixel_id] = (Red, Green, Blue)
    np.show()


def setBrightness(minValue):
    sensorSelect.write_digital(0)
    brightnessLeft = lightSensor.read_analog()
    sensorSelect.write_digital(1)
    brightnessRight = lightSensor.read_analog()

    brightness = int((brightnessLeft + brightnessRight) / 2)
    brightness = int(brightness / 25)
    if(brightness < minValue):
        brightness = minValue
        return brightness


def lineDetector(side):  # 0 == left, 1 == right
    if(side == 0):
        isLine = leftLineSensor.read_digital()
    else:
        isLine = rightLineSensor.read_digital()

    if(isLine == 1):  # Sensor can see the line
        return True
    else:
        return False

def move(_leftSpeed, _rightSpeed, _leftDirection, _rightDirection):
    # speed values between 1 - 1023
    # smaller values == faster speed moving backwards
    # Smaller values == lower speeds when moving forwards
    # direction 0 == forwards, 1 == backwards
    leftSpeed.write_analog(_leftSpeed)  # Set the speed of left motor
    rightSpeed.write_analog(_rightSpeed)  # Set the speed of right motor
    if (_leftDirection != 2):
        leftDirection.write_digital(_leftDirection)  # left motor
        rightDirection.write_digital(_rightDirection)  # right motor


def drive(speed):
    if (speed > 0):
        move(speed, speed, 0, 0)  # move the motors forwards
    else:
        speed = 1023 + speed
        move(speed, speed, 1, 1)  # move the motors backwards


def sharpRight():
    move(100, 1023 + -200, 0, 1)


def sharpLeft():
    move(1023 + -200, 100, 1, 0)


def gentleRight():
    move(200, 0, 0, 0)


def gentleLeft():
    move(0, 200, 0, 0)


def coast():
    move(0, 0, 2, 2)


def stop():
    move(0, 0, 0, 0)


sleep(1000)
while True:
    isLeftLine = lineDetector(0)
    isRightLine = lineDetector(1)
    if(isLeftLine is True) and (isRightLine is False):
        leftLights(setBrightness(1), 0, 0)
        stop()
        sleep(50)
        sharpLeft()
        sleep(200)
        stop()
        sleep(50)
        while(lineDetector(0) is True):
            leftLights(setBrightness(1), 0, 0)
            gentleLeft()

    elif(isRightLine is True) and (isLeftLine is False):
        rightLights(setBrightness(1), 0, 0)
        stop()
        sleep(50)
        sharpRight()
        sleep(200)
        stop()
        sleep(50)
        while(lineDetector(1) is True):
            rightLights(setBrightness(1), 0, 0)
            gentleRight()

    elif(isRightLine is False) and (isLeftLine is False):
        leftLights(0, setBrightness(1), 0)
        rightLights(0, setBrightness(1), 0)
        drive(150)
    else:
        leftLights(0, setBrightness(1), setBrightness(1))
        rightLights(0, setBrightness(1), setBrightness(1))
        stop()
        sleep(200)
```
Nadat wij deze code weer succesvol hebben kunnen uitvoeren zijn we begonnen aan het maken van de code die gebruik zal maken van de ulrasonesensor. Deze code zou er voor moeten zorgen dat de robot zelfstandig (zonder lijn) zich kan weten te manoeuvreren. dit is ons jammer genoeg niet gelukt, omdat we de ulrasonesensor niet aan de praat kregen.

### De dingen die we volgende keer beter kunnen doen zijn:

eerder beginnen aan het expirimenteren met de ulrasonesensor, want die kregen we niet aan de praat en als we meer tijd er in haddeb gestoken denk ik dat we het uiteindelijk wel haddeb uitgevogelt.

voor de rest vind ik dat we dit project goed hebben gedaan en iets meer te weten zijn gekomen hoe je de Microbot kan programmeren met Python.

## Machine learning 

Wij hebben dit blok gekozen voor het project machine learning in dit project gaan Lucien en ik een AI maken die kan leren Flappy bird te spelen. We zijn begonnen met het maken van het spel Flappy bird en daarna met het implanteren van de Neat AI. Het maken van Flappy bird ging met vallen en opstaan, maar we kwamen er uiteindelijk natuurlijk wel uit. Daarna hebben wij de Neat AI in onze flappy bird geimplanteerd. Dit was voor ons allebij iets nieuws, maar raar genoeg ging dit super makkelijk dit is de code die we gemaakt hebben:
```
import pygame
import neat
import time
import os
import random
pygame.font.init()

WIN_WIDTH = 600  
WIN_HEIGHT = 800


BIRD_IMGS = [pygame.transform.scale2x(pygame.image.load(os.path.join("images", "bird1.png"))), pygame.transform.scale2x(pygame.image.load(os.path.join("images", "bird2.png"))), pygame.transform.scale2x(pygame.image.load(os.path.join("images", "bird3.png")))]
PIPE_IMG = pygame.transform.scale2x(pygame.image.load(os.path.join("images", "pipe.png")))
GROUND_IMG = pygame.transform.scale2x(pygame.image.load(os.path.join("images", "base.png")))
BG_IMG = pygame.transform.scale2x(pygame.image.load(os.path.join("images", "bg.png")))

STAT_FONT = pygame.font.SysFont("comicsans", 50)


class Bird:
    IMGS = BIRD_IMGS
    MAX_ROTATION = 25
    ROT_VEL = 20
    ANIMATION_TIME = 5

    def _init_(self, x, y):
        self.x = x
        self.y = y
        self.tilt = 0
        self.tick_count = 0
        self.velocity = 0
        self.height = self.y
        self.img_count = 0;
        self.img = self.IMGS[0]

    def jump(self):
        self.velocity = -10.5
        self.tick_count = 0
        self.height = self.y

    def  move(self):
       self.tick_count += 1

       d = self.velocity*self.tick_count + 1.5*self.tick_count**2

       if d >= 16:
           d = 16

       if d < 0:
           d-= 2

       self.y = self.y + d

       if d < 0 or self.y < self.height - 20:
           if self.tilt < self.MAX_ROTATION:
               self.tilt = self.MAX_ROTATION
       else: 
           if self.tilt > -70:
                   self.tilt -= self.ROT_VEL

    def draw(self, win):
        self.img_count += 1

        if self.img_count < self.ANIMATION_TIME:
            self.img = self.IMGS[0]
        elif self.img_count < self.ANIMATION_TIME * 2:
            self.img = self.IMGS[1]
        elif self.img_count < self.ANIMATION_TIME * 3:
            self.img = self.IMGS[2]
        elif self.img_count < self.ANIMATION_TIME * 4:
            self.img = self.IMGS[1]
        elif self.img_count == self.ANIMATION_TIME * 4 + 1:
            self.img = self.IMGS[0]
            self.img_count = 0

        if self.tilt <= -80:
            self.img = self.IMGS[1]
            self.img_count = self.ANIMATION_TIME*2

        rotated_image = pygame.transform.rotate(self.img, self.tilt)
        new_rect = rotated_image.get_rect(center = self.img.get_rect(topleft = (self.x, self.y)).center)
        win.blit(rotated_image, new_rect.topleft)

    def get_mask(self):
        return pygame.mask.from_surface(self.img)


class Pipe:
    GAP = 200
    VEL = 5

    def _init_(self, x):
        self.x = x
        self.height = 0

        self.top = 0
        self.bottom = 0
        self.PIPE_TOP = pygame.transform.flip(PIPE_IMG, False, True)
        self.PIPE_BOTTOM = PIPE_IMG

        self.passed = False
        self.set_height()

    def set_height(self):
        self.height = random.randrange(50, 450)
        self.top = self.height - self.PIPE_TOP.get_height()
        self.bottom = self.height + self.GAP

    def move(self):
        self.x -= self.VEL

    def draw(self, win):
        win.blit(self.PIPE_TOP, (self.x, self.top))
        win.blit(self.PIPE_BOTTOM, (self.x, self.bottom))

    def collide(self, bird, win):
        bird_mask = bird.get_mask()
        top_mask = pygame.mask.from_surface(self.PIPE_TOP)
        bottom_mask = pygame.mask.from_surface(self.PIPE_BOTTOM)

        top_offset = (self.x - bird.x, self.top - round(bird.y))
        bottom_offset = (self.x - bird.x, self.bottom - round(bird.y))

        b_point = bird_mask.overlap(bottom_mask, bottom_offset)
        t_point = bird_mask.overlap(top_mask, top_offset)

        if t_point or b_point:
            return True

        return False


class Base:
    VEL = 5
    WIDTH = GROUND_IMG.get_width()
    IMG = GROUND_IMG

    def _init_ (self, y):
        self.y = y
        self.x1 = 0
        self.x2 = self.WIDTH

    def move(self):
        self.x1 -= self.VEL
        self.x2 -= self.VEL

        if self.x1 + self.WIDTH < 0:
            self.x1 = self.x2 + self.WIDTH

        if self.x2 + self.WIDTH < 0:
            self.x2 = self.x1 + self.WIDTH

    def draw(self, win):
        win.blit(self.IMG, (self.x1, self.y))
        win.blit(self.IMG, (self.x2, self.y))


def draw_window(win, birds, pipes, base, score):
    win.blit(BG_IMG, (0,0))

    for pipe in pipes:
           pipe.draw(win)

    text = STAT_FONT.render("Score " + str(score), 1, (255,255,255))
    win.blit(text, (WIN_WIDTH - 10 - text.get_width(), 10))


    base.draw(win)
    for bird in birds:
        bird.draw(win)
    pygame.display.update()

def main(genomes, config):
    nets = []
    ge = []
    birds = []

    for _, g in genomes:
        net = neat.nn.FeedForwardNetwork.create(g, config)
        nets.append(net)
        birds.append(Bird(230, 350))
        g.fitness = 0
        ge.append(g)


    base = Base(730)
    pipes = [Pipe(600)]
    win = pygame.display.set_mode((WIN_WIDTH, WIN_HEIGHT))
    run = True
    clock = pygame.time.Clock()

    score = 0

    while run:
        clock.tick(30)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
                pygame.quit()
                quit()

        pipe_ind = 0
        if len(birds) > 0:
            if len(pipes) > 1 and birds[0].x > pipes[0].x + pipes[0].PIPE_TOP.get_width():
                pipe_ind = 1
        else:
            run = False
            break

        for x, bird in enumerate(birds):
            bird.move()
            ge[x].fitness += 0.1

            output = nets[x].activate((bird.y, abs(bird.y - pipes[pipe_ind].height), abs(bird.y - pipes[pipe_ind].bottom)))

            if output[0] > 0.5:
                bird.jump()

        add_pipe = False  
        rem = []
        for pipe in pipes:
            for x, bird in enumerate(birds):
                if pipe.collide(bird, win):
                    ge[x].fitness -= 1
                    birds.pop(x)
                    nets.pop(x)
                    ge.pop(x)


                if not pipe.passed and pipe.x < bird.x:
                    pipe.passed = True
                    add_pipe = True

            if pipe.x + pipe.PIPE_TOP.get_width() < 0:
                rem.append(pipe)

            pipe.move()

        if add_pipe:
            score += 1
            for g in ge:
                g.fitness += 5
            pipes.append(Pipe(600))

        for r in rem:
            pipes.remove(r)
        
        for x, bird in enumerate(birds):
            if bird.y + bird.img.get_height() >= 730 or bird.y < 0:
               birds.pop(x)
               nets.pop(x)
               ge.pop(x)

        base.move()
        draw_window(win, birds, pipes, base, score)

def run(config_path):
    config = neat.config.Config(neat.DefaultGenome, neat.DefaultReproduction, neat.DefaultSpeciesSet, neat.DefaultStagnation, config_path)

    p = neat.Population(config)

    p.add_reporter(neat.StdOutReporter(True))
    stats = neat.StatisticsReporter()
    p.add_reporter(stats)
    
    winner = p.run(main,50)

if _name_ == "_main_":
    local_dir = os.path.dirname(_file_)
    config_path = os.path.join(local_dir, "config-feedforward.txt")
    run(config_path)
```


## Corona kaart

In blok 4 moesten we een project maken dat te maken had met data en corona. Lucien en ik hadden daaral gelijk een heel duidelijk idee bij. We wouden een kaart maken die in een soort timelapse liet zien hoe corona zich over de wereld heeft verspreid. We hebben dit gedaan door een hoogkwaliteit kaart te downloaden en vervolgens een Json file te downloaden met alle data in verband met corona die we eventueel nodig zouden hebben. Daarna hebben we voor elk land de coördinaten op de kaart gegeven. daarna moesten we er nog voor zorgen dat er een timelapse kwam en dat de cirkels van grootte en kleur veranderden. dat hebben we gedaan met deze code:

```
using System.Collections;
using System.Collections.Generic;
using SimpleJSON;
using System;

public class WorldScript : MonoBehaviour
{

    public static WorldScript Instance { set; get; }

    Animator anim;

    public Gradient amount;

    [System.Serializable]
    public class Date {

        public string date;
        public int worldData;
        public List<string> countryName = new List<string>();
        public List<int> countryData = new List<int>();
    }

    public List<Date> date;
    public List<GameObject> Country;
    public GameObject Holder;

    int maxInfections = 0;
    public int currentDate = 120;
    public GameObject Template;
    public Camera mainCam;
    public InputField input;
    public Text maxInfection;
    public Text dateText;
    public Slider timeline;
    public LineRenderer graph;
    bool play = false;

    // Start is called before the first frame update
    void Start()
    {
        Instance = this;

        int currentDate = -1;
        string[] jsonFile = System.IO.File.ReadAllLines("csvjson.json");
        for (int i = 0; i < jsonFile.Length; i++)
        {
            string line = jsonFile[i];
            if (line.Contains("date")) {
                date.Add(new Date());
                currentDate += 1;
                date[currentDate].date = line.Substring(line.IndexOf("\"date\": \"") + 9, 10);
            }
            else if (line.Contains("World"))
            {
                date[currentDate].worldData = int.Parse(line.Substring(line.IndexOf("\"World\": ") + 9, line.Length - 14));
            }
            else if (line.Contains("{") || line.Contains("}") || line.Contains("[") || line.Contains("]"))
            {

            }

            else
            {
                string country = "";
                string number = "";
                for (int t = line.IndexOf("\"") + 1; t < line.Length; t++)
                {
                    if (line[t] == '\"') t = 1000;
                    else country += line[t];
                }
                date[currentDate].countryName.Add(country);
                for (int n = line.Length - 2; n > 0; n--)
                {
                    if (line[n] == '\"' || line[n] == ' ') n = 0;
                    else number += line[n];

                }
                if (number == "") date[currentDate].countryData.Add(0);
                else date[currentDate].countryData.Add(int.Parse(ReverseString(number)));
            }
        }
        for (int c = 0; c < Holder.transform.childCount; c++)
        {
            Country.Add(Holder.transform.GetChild(c).gameObject);
        }
        anim = GetComponent<Animator>();

        timeline.maxValue = date.Count -2;
        maxInfections = 500000;
    }

    // Update is called once per frame
    void Update()
    {
        maxInfection.text = "COVID-19 infections:\n" + date[currentDate].worldData;
        dateText.text = new DateTime(int.Parse(date[currentDate].date.Split('-')[0]), int.Parse(date[currentDate].date.Split('-')[1]), int.Parse(date[currentDate].date.Split('-')[2])).ToString("m");
        timeline.value = currentDate;
    }

    public void Play()
    {
        play = !play;
        if (!play) StopCoroutine(Dates());
        if(play) StartCoroutine(Dates());
        
    }

    public IEnumerator Dates()
    {
        while(play && currentDate < date.Count -2)
        {
            LoadData(currentDate);
            yield return new WaitForSeconds(0.1f);
            currentDate += 1;
        }
        
    }

    public void Timeline()
    {
        currentDate = (int)timeline.value;
        LoadData(currentDate);
    }

    public void Continent()
    {
        anim.SetBool(EventSystem.current.currentSelectedGameObject.name, !anim.GetBool(EventSystem.current.currentSelectedGameObject.name));
    }

    public string ReverseString(string s)
    {
        char[] arr = s.ToCharArray();
        Array.Reverse(arr);
        return new string(arr);
    }

    public void ChangeScale()
    {
        int.TryParse(input.text, out maxInfections);
        LoadData(currentDate);
    }

    public void LoadData(int time)
    {
        float Xinterval = 400f / currentDate;
        graph.positionCount = currentDate;
        for (int d = 0; d < currentDate; d++)
        {
            graph.SetPosition(d, new Vector3(Xinterval * d, ((float)date[d].worldData / (float)date[currentDate].worldData) * 400f, 0));
        }
        float percentage = 0;
        for(int i = 0; i < Country.Count; i++)
        {
            for(int c = 0; c < date[time].countryName.Count; c++)
            {
                if (date[time].countryName[c] == Country[i].name)
                {
                    percentage = Mathf.Log(date[time].countryData[c]) / Mathf.Log(maxInfections);
                    if (percentage > 1f) percentage = 1f;
                    if (percentage < 0f) percentage = 0f;
                    Country[i].GetComponent<Image>().color = amount.Evaluate(percentage);
                    Country[i].transform.localScale = new Vector3(percentage * 4f + 1, percentage * 4f + 1, 1);
                }
            }
        }
    }
}
```
