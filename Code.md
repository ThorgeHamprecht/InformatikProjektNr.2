# Infinity Throw
## Inhhaltsverzeichnis
1. [Projektseite](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Projektseite.md)
2. [Code](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Code.md)
3. [Protokollseite](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Protokollseite.md)

## Der finale Code 
Der Code ist zum größten Teil von uns verfasst. Nur Zeilen, vor denen mit # englische Erklärungen stehen stammen nicht von uns. Der Code zur Ballerkennung stammt von  Adrian Rosebrock aus seinem [Blog](https://pyimagesearch.com/2015/09/14/ball-tracking-with-opencv/) zum Thema "Ball Tracking with OpenCV". Dabei wird die [OpenCV-Lybrary](https://opencv.org/) verwendet. Der zusätzlich zum Bestimmen des Farbraumes des balles verwendete Code stammt von hariangr. Dieser hat ein Github-Repository zu seinem [HSV-Range-Tool](https://github.com/hariangr/HsvRangeTool).



```python
# importieren der notwendigen Bibliotheken

import argparse
import time
from collections import deque
import random
import pygame.mixer
from pygame.locals import *

import cv2
import imutils
import math
import numpy as np
from imutils.video import VideoStream
import matplotlib.pyplot as plt

pygame.init() #Die Pygame Library aktivieren

#Variabeln für Simulation definieren
a = 0
b = 0
c = 0
posX = 0
posY = 0


# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-v", "--video",
	help="path to the (optional) video file")
ap.add_argument("-b", "--buffer", type=int, default=64,
	help="max buffer size")
args = vars(ap.parse_args())
# define the lower and upper boundaries of the "green"
# ball in the HSV color space, then initialize the
# list of tracked points
greenLower = (86, 39, 97)
greenUpper = (179, 228, 255)
x = 0
y = 0
Streckfaktor = 0.5 / 0.627
Zusatzhoehe = -100
Korrektur = 0.3
listX = deque()
listY = deque()
messEinsX = 0
messEinsXconv = 0
messEinsY = 0
messEinsYconv = 0
messZweiX = 0
messZweiY = 0
wdh = 0
objektErkannt = False


pts = deque(maxlen=args["buffer"])

# if a video path was not supplied, grab the reference
# to the webcam
if not args.get("video", False):
	vs = VideoStream(src=0).start()
# otherwise, grab a reference to the video file
else:
	vs = cv2.VideoCapture(args["video"])
# allow the camera or video file to warm up
time.sleep(2.0)
# keep looping
while True:
	# grab the current frame
	frame = vs.read()
	# handle the frame from VideoCapture or VideoStream
	frame = frame[1] if args.get("video", False) else frame
	# if we are viewing a video and we did not grab a frame,
	# then we have reached the end of the video
	if frame is None:
		break
	# resize the frame, blur it, and convert it to the HSV
	# color space
	frame = imutils.resize(frame, width=600)
	blurred = cv2.GaussianBlur(frame, (11, 11), 0)
	hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)
	# construct a mask for the color "green", then perform
	# a series of dilations and erosions to remove any small
	# blobs left in the mask
	mask = cv2.inRange(hsv, greenLower, greenUpper)
	mask = cv2.erode(mask, None, iterations=2)
	mask = cv2.dilate(mask, None, iterations=2)
	# find contours in the mask and initialize the current
	# (x, y) center of the ball
	cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
							cv2.CHAIN_APPROX_SIMPLE)
	cnts = imutils.grab_contours(cnts)
	center = None
	# only proceed if at least one contour was found
	if len(cnts) > 0:
		# find the largest contour in the mask, then use
		# it to compute the minimum enclosing circle and
		# centroid
		c = max(cnts, key=cv2.contourArea)
		((x, y), radius) = cv2.minEnclosingCircle(c)
		M = cv2.moments(c)
		center = (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))
		# only proceed if the radius meets a minimum size
		if radius > 7:
			# draw the circle and centroid on the frame,
			# then update the list of tracked points
			cv2.circle(frame, (int(x), int(y)), int(radius),
					   (0, 255, 255), 2)
			cv2.circle(frame, center, 5, (0, 0, 255), -1)
			objektErkannt = True
	# Koordinaten Erkennung

	if wdh < 120 and objektErkannt == True:
		wdh = wdh + 1 # zählt die Anzahl an Durchläufen des Loops (60 Durchläufe pro Sekunde)
		if wdh == 5 and objektErkannt == True: # Anzahl an Durchläufen nachdem Erkennen des Obejekts für Aufnahme des Messpunkts

			# Ersten Messpunkt für LGS aufnehmen
			x1 = (x / Streckfaktor)-108
			x1Quadrat = x1 ** 2
			y1 = (y / Streckfaktor) + Zusatzhoehe
			start = time.time() # Zeitmessung für Zeitabstand zwischen erstem und zweitem Messpunkt beginnen
			print("Erste Messung erfolgreich")
			
			
		if wdh == 20: # Anzahl an Durchläufen nachdem Erkennen des Obejekts für Aufnahme des Messpunkts

			#Zweiten Messpunkt für LGS aufnehmen
			x2 = (x / Streckfaktor)-108
			x2Quadrat = x2 ** 2
			y2 = (y / Streckfaktor) + Zusatzhoehe
			ende = time.time() # Zeitmessung stoppen
			t1 = (ende - start) # Vergangene Zeit ausrechnen für t1
			start = time.time() # Zeitmessung für t2 starten
			print("Zweite Messung erfolgreich")

		if wdh == 35:
			#Dritten Messpunkt für LGS aufnehmen
			x3 = (x / Streckfaktor)-108
			x3Quadrat = x3 ** 2
			y3 = (y/ Streckfaktor) + Zusatzhoehe

			print("x1", x1)
			print("x2", x2)
			print("x3", x3)
			print("y1", y1)
			print("y2", y2)
			print("y3", y3)


			ende = time.time()  # Zeitmessung stoppen
			t2 = (ende - start) # Zeitintervall t2 berechnen

			print("Dritte Messung (nur LGS) erfolgreich")

			#Dinge für Simulation (Vorberechnungen)
			simUpdate = True
			t = 0
			posX = 0
			posY = 0
			posFass = 800
			kollisionAnzahl = 0
			scoreNumb = 0
			# definieren des radiusBall des Kreises
			radiusBall = 20
			durchläufe = 0

			ballXlinks = posX - radiusBall, posY
			ballXrechts = posX + radiusBall, posY
			ballAmBoden = False

			zähler = 0
			zufallX = 0

			
			
			# Bilder für Meter Anzeige importieren, obsolet, erklärung: durch schrift ersetzt
			meterImage = pygame.image.load('50cm.png')
			meterImage = pygame.transform.scale(meterImage,(250 / 5, 150 / 5))  # das Bild aud die richtige Größe skalieren
			# Fass bild importieren
			fassImage = pygame.image.load('Fass.png')
			fassImage = pygame.transform.scale(fassImage,(116 / 1.25, 128 / 1.25))  # das Bild aud die richtige Größe skalieren
			# Fass Kollision mit Ball, dafür Rechteckige Hitbox

			fassKollision = pygame.Rect((20, 50), (50, 100))

			posYstring = "{:4.2f}".format(posY)
			# Texte definieren
			test = "test"
			score = "Score:"



			# vordefienieren von Farben im RGB Raum
			schwarz = (0, 0, 0)
			weiß = (255, 255, 255)
			pastellBlau = (106, 147, 176)
			grau = (136, 136, 136)
			rosa = (234, 137, 154)
			hellGrau = (196, 196, 204)
			grün = (50, 205, 50)
			rot = (255, 48, 48)

			# Schriftart importieren
			schriftArt = pygame.font.Font('freesansbold.ttf', 32)
			schriftArtKlein = pygame.font.Font("freesansbold.ttf", 14)
			schriftArtScore = pygame.font.Font("freesansbold.ttf", 32)
			schriftArtTreffer = pygame.font.Font("freesansbold.ttf", 50)

			#Text auf objekt rendern
			text = schriftArt.render(test, True, schwarz, weiß)
			scoreText = schriftArtScore.render(score, True, schwarz, pastellBlau)
			trefferText = schriftArtTreffer.render("Treffer!", True, grün, pastellBlau)
			danebenText = schriftArtTreffer.render(("Daneben!"), True, rot, pastellBlau)
			länge50cm = schriftArtKlein.render("0.5m", True, schwarz, hellGrau)
			länge100cm = schriftArtKlein.render("1.0m", True, schwarz, hellGrau)
			länge150cm = schriftArtKlein.render("1.5m", True, schwarz, hellGrau)
			länge200cm = schriftArtKlein.render("2.0m", True, schwarz, hellGrau)
			länge250cm = schriftArtKlein.render("2.5m", True, schwarz, hellGrau)
			länge300cm = schriftArtKlein.render("3.0m", True, schwarz, hellGrau)
			länge350cm = schriftArtKlein.render("3.5m", True, schwarz, hellGrau)
			länge400cm = schriftArtKlein.render("4.0m", True, schwarz, hellGrau)
			länge450cm = schriftArtKlein.render("4.5m", True, schwarz, hellGrau)
			länge500cm = schriftArtKlein.render("5.0m", True, schwarz, hellGrau)
			länge550cm = schriftArtKlein.render("5.5m", True, schwarz, hellGrau)
			länge600cm = schriftArtKlein.render("6.0m", True, schwarz, hellGrau)

			# Objekt erstellen, auf den der Text gerendert wird
			textObjekt = text.get_rect()
			scoreObjekt = scoreText.get_rect()
			trefferObjekt = trefferText.get_rect()
			danebenObjekt = danebenText.get_rect()
			l50cmObjekt = länge50cm.get_rect()
			l100cmObjekt = länge100cm.get_rect()
			l150cmObjekt = länge150cm.get_rect()
			l200cmObjekt = länge200cm.get_rect()
			l250cmObjekt = länge250cm.get_rect()
			l300cmObjekt = länge300cm.get_rect()
			l350cmObjekt = länge350cm.get_rect()
			l400cmObjekt = länge400cm.get_rect()
			l450cmObjekt = länge450cm.get_rect()
			l500cmObjekt = länge500cm.get_rect()
			l550cmObjekt = länge550cm.get_rect()
			l600cmObjekt = länge600cm.get_rect()

			# Mittelpunkt des Textobjekts bestimmen
			textObjekt.center = (30, 16)
			scoreObjekt.center = (1000, 30)
			trefferObjekt.center = (600, 400)
			danebenObjekt.center = (600, 400)
			l50cmObjekt.center = (100, 792)
			l100cmObjekt.center = (200, 792)
			l150cmObjekt.center = (300, 792)
			l200cmObjekt.center = (400, 792)
			l250cmObjekt.center = (500, 792)
			l300cmObjekt.center = (600, 792)
			l350cmObjekt.center = (700, 792)
			l400cmObjekt.center = (800, 792)
			l450cmObjekt.center = (900, 792)
			l500cmObjekt.center = (1000, 792)
			l550cmObjekt.center = (1100, 792)
			l600cmObjekt.center = (1200, 792)

			screen_size = screen_width, screen_height = 1200, 800
			screen = pygame.display.set_mode(screen_size)
			pygame.display.set_caption('Schräger Wurf Simulation')

			clock = pygame.time.Clock()
			fpsLimit = 60

			# Geschwindigkeit in x richtung berechnen
			xGeschwindigkeit = (x3 - x1)/((t1 + t2) / 2)

			# LGS Lösen
			A = np.array([[1, x1, x1Quadrat], [1, x2, x2Quadrat], [1, x3, x3Quadrat]])
			B = np.array([y1, y2, y3])
			X = np.linalg.solve(A, B)

			a = X[0]
			b = X[1]
			c = X[2]

			xLGS = np.arange(0, 6+ 0.05, 0.01)
			yLGS = (a + b * (xLGS + 0.335) + c * (xLGS + 0.335) ** 2)

			plt.plot(xLGS, yLGS)

			runSim = True
			break

	# update the points queue
	pts.appendleft(center)
# loop over the set of tracked points
	for i in range(1, len(pts)):
		# if either of the tracked points are None, ignore
		# them
		if pts[i - 1] is None or pts[i] is None:
			continue
		# otherwise, compute the thickness of the line and
		# draw the connecting lines
		thickness = int(np.sqrt(args["buffer"] / float(i + 1)) * 2.5)
		cv2.line(frame, pts[i - 1], pts[i], (0, 0, 255), thickness)
	# show the frame to our screen
	cv2.imshow("Frame", frame)
	key = cv2.waitKey(1) & 0xFF
	# if the 'q' key is pressed, stop the loop
	if key == ord("q"):
		break
# if we are not using a video file, stop the camera video stream
if not args.get("video", False):
	vs.stop()
# otherwise, release the camera
else:
	vs.release()
# close all windows
cv2.destroyAllWindows()



if runSim == True:
	# Der loop in dem das Programm läuft
	while simUpdate == True:
		clock.tick(fpsLimit)  # definiert, dass der Loop 60 mal die Sekunde läuft
		# test
		posFassint = int((posFass + 32) / 2)
		posFassstring = str(posFassint)
		posXint = int(posX / 2)
		posXstring = str(posXint)

		text = schriftArt.render(("Entfernung zum Fass: " + posFassstring + "cm"), True, schwarz, weiß)

		# Score Anzeige Updaten
		score = ("Score: " + str(scoreNumb))
		scoreText = schriftArtScore.render(score, True, schwarz, pastellBlau)


		#
		fassKollision = pygame.Rect((posFass + 9, 660), (75, 100))
		punkt = (posX, posY)
		kollisionMitte = fassKollision.collidepoint(punkt)
		kollisionLinks = fassKollision.collidepoint(ballXlinks)
		kollisionRechts = fassKollision.collidepoint(ballXrechts)
		if kollisionMitte == True:
			print("getroffen")
			kollisionAnzahl = kollisionAnzahl + 1
			if kollisionAnzahl == 1:
				scoreNumb = scoreNumb + 1

			if kollisionRechts == True:
				print("getroffen")
			if kollisionLinks == True:
				print("getroffen")

		screen.fill(pastellBlau)  # Bildschirm jeden Frame neu schwarz färben

		pygame.draw.rect(screen, grau, (0, 760, 1200, 40))  # Den Boden jeden Frame platzieren

		pygame.draw.rect(screen, weiß, (200, 760, 200, 40))  # Die Skalierungsfelder über den boden legen
		pygame.draw.rect(screen, weiß, (600, 760, 200, 40))
		pygame.draw.rect(screen, weiß, (1000, 760, 200, 40))

		pygame.draw.rect(screen, hellGrau, (0, 780, 1200, 20))  # untere Leiste einzeichnen
		# Feinskalierung einfügen
		pygame.draw.rect(screen, schwarz, (19, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (39, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (59, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (79, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (99, 760, 2, 20))  # 50cm Linie, etwas höher
		pygame.draw.rect(screen, schwarz, (119, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (139, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (159, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (179, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (199, 760, 2, 20))  # 1m
		pygame.draw.rect(screen, schwarz, (219, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (239, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (259, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (279, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (299, 760, 2, 20))  # 1.50m
		pygame.draw.rect(screen, schwarz, (319, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (339, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (359, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (379, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (399, 760, 2, 20))  # 2m
		pygame.draw.rect(screen, schwarz, (419, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (439, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (459, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (479, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (499, 760, 2, 20))  # 2.50m
		pygame.draw.rect(screen, schwarz, (519, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (539, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (559, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (579, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (599, 760, 2, 20))  # 3m
		pygame.draw.rect(screen, schwarz, (619, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (639, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (659, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (679, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (699, 760, 2, 20))  # 3.50m
		pygame.draw.rect(screen, schwarz, (719, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (739, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (759, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (779, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (799, 760, 2, 20))  # 4m
		pygame.draw.rect(screen, schwarz, (819, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (839, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (859, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (879, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (899, 760, 2, 20))  # 4.5m
		pygame.draw.rect(screen, schwarz, (919, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (939, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (959, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (979, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (999, 760, 2, 20))  # 5m
		pygame.draw.rect(screen, schwarz, (1019, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1039, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1059, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1079, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1099, 760, 2, 20))  # 5.5m
		pygame.draw.rect(screen, schwarz, (1119, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1139, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1159, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1179, 760, 2, 15))
		pygame.draw.rect(screen, schwarz, (1199, 760, 2, 20))  # 6m
		# Zahlenmarkierungen für Meter
		screen.blit(länge50cm, l50cmObjekt)
		screen.blit(länge100cm, l100cmObjekt)
		screen.blit(länge150cm, l150cmObjekt)
		screen.blit(länge200cm, l200cmObjekt)
		screen.blit(länge250cm, l250cmObjekt)
		screen.blit(länge300cm, l300cmObjekt)
		screen.blit(länge350cm, l350cmObjekt)
		screen.blit(länge400cm, l400cmObjekt)
		screen.blit(länge450cm, l450cmObjekt)
		screen.blit(länge500cm, l500cmObjekt)
		screen.blit(länge550cm, l550cmObjekt)
		screen.blit(länge600cm, l600cmObjekt)

		pygame.draw.rect(screen, schwarz, fassKollision)

		# Positionsänderung des Kreises gemäß der Bahnkurve und der Zeit

		# Funktion aus Werten für LGS erstellen
		posX = posX + 1/60 * xGeschwindigkeit
		posY = (a + b * (posX) + c * (posX) ** 2)
		if posY >= 740:
			posY = 740
			if ballAmBoden == False:
				xWurfweite = posX
			posX = xWurfweite
			ballAmBoden = True
			if ballAmBoden == True and kollisionMitte == False:
				danebenText = schriftArtTreffer.render(("Daneben! ( " + posXstring + " cm )"), True, rot, pastellBlau)
				screen.blit(danebenText, danebenObjekt)

		pygame.draw.circle(screen, rosa, (posX, posY), radiusBall)




		t = t + 1 / 60  # Zeit seit start der Simulation in Sekunden
		
		# Fass Anzeigen
		screen.blit(fassImage,(posFass, 658))  # x-Koordinate darf zwischen 0 und 1120 sein, random number generation
		# Fass position ändern
		zähler = zähler + 1
		if zähler == 120:
			zähler = 0
			zufallX = random.uniform(0, 1120)  # Zufällige Zahl zwischen 0 und 1120

		# text an oberster Stelle rendern
		screen.blit(text, textObjekt)
		screen.blit(scoreText, scoreObjekt)

		if kollisionAnzahl > 0:
			blinken = 1
			if blinken == 1:
				if kollisionAnzahl < 30:
					screen.blit(trefferText, trefferObjekt)
				if kollisionAnzahl == 60:
					kollisionAnzahl = 1



		# Bildschirm updaten
		pygame.display.flip()

		for event in pygame.event.get():
			if event.type == pygame.QUIT:
				simUpdate = False

	pygame.quit()
print("Erster Zeitintervall:",t1, "s")
print("Zweiter Zeitintervall:",t2, "s")


# Neue Methode zur Bestimmung der Gleichung mit LGS
A = np.array([[1, x1, x1Quadrat], [1, x2, x2Quadrat], [1, x3, x3Quadrat]])
B = np.array([y1, y2, y3])
X = np.linalg.solve(A,B)

a = X[0]
b = X[1]
c = X[2]

Wurfweite = ((-a*c+0.25*b**2)/(c**2))**0.5+(-b)/(2*c)
print("Wurfweite:", Wurfweite - 0.3, "m")

# x und y für Graphen definieren, x startet von 0, geht bis "Wurfweite" + 5% der Wurfweite, geht in 0.05 Schritten; y ist die Bahnkurvenformel für den schrägen Wurf
xLGS = np.arange(0, Wurfweite + 0.05, 0.01) # + Wurfweite * 0.05
y = (Abwurfhoehe + math.tan(Abwurfwinkel) * xLGS - (g / (2 * (Geschwindigkeit**2) * (math.cos(Abwurfwinkel)**2)) * xLGS**2))



xLGS = np.arange(0, Wurfweite + 0.05, 0.01)
yLGS = (a + b * (xLGS + 0.335) + c * (xLGS + 0.335)**2)
```
## Der OpenCV Ball-Tracking Code aus dem Internet in einem unbearbeitetem Stück 
```python
# import the necessary packages
from collections import deque
from imutils.video import VideoStream
import numpy as np
import argparse
import cv2
import imutils
import time
# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-v", "--video",
	help="path to the (optional) video file")
ap.add_argument("-b", "--buffer", type=int, default=64,
	help="max buffer size")
args = vars(ap.parse_args())
# define the lower and upper boundaries of the "green"
# ball in the HSV color space, then initialize the
# list of tracked points
greenLower = (29, 86, 6)
greenUpper = (64, 255, 255)
pts = deque(maxlen=args["buffer"])
# if a video path was not supplied, grab the reference
# to the webcam
if not args.get("video", False):
	vs = VideoStream(src=0).start()
# otherwise, grab a reference to the video file
else:
	vs = cv2.VideoCapture(args["video"])
# allow the camera or video file to warm up
time.sleep(2.0)
# keep looping
while True:
	# grab the current frame
	frame = vs.read()
	# handle the frame from VideoCapture or VideoStream
	frame = frame[1] if args.get("video", False) else frame
	# if we are viewing a video and we did not grab a frame,
	# then we have reached the end of the video
	if frame is None:
		break
	# resize the frame, blur it, and convert it to the HSV
	# color space
	frame = imutils.resize(frame, width=600)
	blurred = cv2.GaussianBlur(frame, (11, 11), 0)
	hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)
	# construct a mask for the color "green", then perform
	# a series of dilations and erosions to remove any small
	# blobs left in the mask
	mask = cv2.inRange(hsv, greenLower, greenUpper)
	mask = cv2.erode(mask, None, iterations=2)
	mask = cv2.dilate(mask, None, iterations=2)
		# find contours in the mask and initialize the current
	# (x, y) center of the ball
	cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
		cv2.CHAIN_APPROX_SIMPLE)
	cnts = imutils.grab_contours(cnts)
	center = None
	# only proceed if at least one contour was found
	if len(cnts) > 0:
		# find the largest contour in the mask, then use
		# it to compute the minimum enclosing circle and
		# centroid
		c = max(cnts, key=cv2.contourArea)
		((x, y), radius) = cv2.minEnclosingCircle(c)
		M = cv2.moments(c)
		center = (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))
		# only proceed if the radius meets a minimum size
		if radius > 10:
			# draw the circle and centroid on the frame,
			# then update the list of tracked points
			cv2.circle(frame, (int(x), int(y)), int(radius),
				(0, 255, 255), 2)
			cv2.circle(frame, center, 5, (0, 0, 255), -1)
	# update the points queue
	pts.appendleft(center)
	# loop over the set of tracked points
	for i in range(1, len(pts)):
		# if either of the tracked points are None, ignore
		# them
		if pts[i - 1] is None or pts[i] is None:
			continue
		# otherwise, compute the thickness of the line and
		# draw the connecting lines
		thickness = int(np.sqrt(args["buffer"] / float(i + 1)) * 2.5)
		cv2.line(frame, pts[i - 1], pts[i], (0, 0, 255), thickness)
	# show the frame to our screen
	cv2.imshow("Frame", frame)
	key = cv2.waitKey(1) & 0xFF
	# if the 'q' key is pressed, stop the loop
	if key == ord("q"):
		break
# if we are not using a video file, stop the camera video stream
if not args.get("video", False):
	vs.stop()
# otherwise, release the camera
else:
	vs.release()
# close all windows
cv2.destroyAllWindows()
```

## Der Range-Test Code aus dem Internet zur Farbbestimmung des Balls
```python
# finding hsv range of target object(pen)
import cv2
import numpy as np
import time


# A required callback method that goes into the trackbar function.
def nothing(x):
    pass


# Initializing the webcam feed.
cap = cv2.VideoCapture(0)
cap.set(3, 1280)
cap.set(4, 720)

# Create a window named trackbars.
cv2.namedWindow("Trackbars")

# Now create 6 trackbars that will control the lower and upper range of
# H,S and V channels. The Arguments are like this: Name of trackbar,
# window name, range,callback function. For Hue the range is 0-179 and
# for S,V its 0-255.
cv2.createTrackbar("L - H", "Trackbars", 0, 179, nothing)
cv2.createTrackbar("L - S", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("L - V", "Trackbars", 0, 255, nothing)
cv2.createTrackbar("U - H", "Trackbars", 179, 179, nothing)
cv2.createTrackbar("U - S", "Trackbars", 255, 255, nothing)
cv2.createTrackbar("U - V", "Trackbars", 255, 255, nothing)

while True:

    # Start reading the webcam feed frame by frame.
    ret, frame = cap.read()
    if not ret:
        break
    # Flip the frame horizontally (Not required)
    frame = cv2.flip(frame, 1)

    # Convert the BGR image to HSV image.
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    # Get the new values of the trackbar in real time as the user changes
    # them
    l_h = cv2.getTrackbarPos("L - H", "Trackbars")
    l_s = cv2.getTrackbarPos("L - S", "Trackbars")
    l_v = cv2.getTrackbarPos("L - V", "Trackbars")
    u_h = cv2.getTrackbarPos("U - H", "Trackbars")
    u_s = cv2.getTrackbarPos("U - S", "Trackbars")
    u_v = cv2.getTrackbarPos("U - V", "Trackbars")

    # Set the lower and upper HSV range according to the value selected
    # by the trackbar
    lower_range = np.array([l_h, l_s, l_v])
    upper_range = np.array([u_h, u_s, u_v])

    # Filter the image and get the binary mask, where white represents
    # your target color
    mask = cv2.inRange(hsv, lower_range, upper_range)

    # You can also visualize the real part of the target color (Optional)
    res = cv2.bitwise_and(frame, frame, mask=mask)

    # Converting the binary mask to 3 channel image, this is just so
    # we can stack it with the others
    mask_3 = cv2.cvtColor(mask, cv2.COLOR_GRAY2BGR)

    # stack the mask, orginal frame and the filtered result
    stacked = np.hstack((mask_3, frame, res))

    # Show this stacked frame at 40% of the size.
    cv2.imshow('Trackbars', cv2.resize(stacked, None, fx=0.4, fy=0.4))

    # If the user presses ESC then exit the program
    key = cv2.waitKey(1)
    if key == 27:
        break

    # If the user presses `s` then print this array.
    if key == ord('s'):
        thearray = [[l_h, l_s, l_v], [u_h, u_s, u_v]]
        print(thearray)

        # Also save this array as penval.npy
        np.save('hsv_value', thearray)
        break

# Release the camera & destroy the windows.
cap.release()
cv2.destroyAllWindows()
```
