# Projektseite - Infinity Throw


https://user-images.githubusercontent.com/88385813/163590424-2e5dedc1-a03d-461c-b1da-e1c7cf0d4448.mp4





infinitythrow ist ein Augmented Reality Spiel, das einen echten Ballwurf mit dem Ballwurf in einer Simulation verbindet. Grundsätzlich ist es dabei Ziel, den Ball in eine nur in der Simulation vorhandene Tonne zu werfen. Der Ball wird dabei in der realen Welt geworfen und anschließend ein dementsprechender Klon in der Simulation erstellt, der die selben Eigenschaften, also Geschwindigkeit und Bewegungsrichtung wie der echte Ball hat. Dabei gibt es zentral zwei Teile des Programmcodes: Zuerst die Erkennung der Flugkurve des Balls und anschließend die weitere Berechnung derselben in der Simulation und das Erkennen, ob der Ball die Tone getroffen hat.

# Inhaltsverzeichnis
1. [Projektseite](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Projektseite.md)
2. [Code](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Code.md)
3. [Protokollseite](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Protokollseite.md)

## [Python](https://www.python.org/)
<img width="200" alt="Bildschirmfoto 2022-04-18 um 19 35 09" src="https://user-images.githubusercontent.com/88385813/157444258-77318303-ed05-49b7-8ace-10eebca0f0f5.png"> 

Für die Umsetzung unseres Projektes haben wir Python genutzt. Wir haben uns für python entschieden, da Python sich vor allen Dingen durch seine leichte Syntax und vielseitigen Anwendungsgebiete auszeichnet. Dabei kann das Grundgerüst von Python durch viele andere Bibliotheken erweitert und angepasst werden. Da wir zu Anfang unseres projektes noch keine genaue Vorstellung von dem Endprodukt unseres Projekts hatten, stellte Python genau das richtige Grundgerüst dar, um unser Programm später nach unseren Vorstellungen zu erweitern. Des weiteren ist Python auch sehr ähnlich zu C#, eine Programmiersprache mit der wir schon einmal in der Projektfindungsphase des ersten projekts Erfahrungen gemacht hatten.

## [PyCharm](https://www.jetbrains.com/pycharm/)
<img width="200" alt="Bildschirmfoto 2022-04-18 um 19 35 09" src="https://user-images.githubusercontent.com/88385813/163342121-e0e1be82-a348-4d29-a16c-04bcb0c8c442.png">


Für das Schreiben und Ausführen des Codes haben wir uns für für PyCharm entschieden. PyCharm ist relativ ähnlich zu Microsoft Visual Studio. Allerdings ist PyCharm für Python Entwicklung optimiert. In PyCharm kann der Code sowohl geschrieben, als auch ausgeführt werden und das Programm überprüft den Code auch auf Fehler, was das "Debugging" enorm erleichtert. PyCharm wird auch im profressionellem Bereich genutzt ist allerdings für Non-Profit-Projekte kostenlos verfügbar. 

## [OpenCV](https://opencv.org/)
<img width="200" alt="Bildschirmfoto 2022-04-18 um 19 35 09" src="https://user-images.githubusercontent.com/88385813/157444014-ec4f9bcf-7afb-4fd1-bd95-0dfce1b4254e.png">


Für die Umsetzung von infinitythrow mussten wir vor allen anderen Dingen ein großes Problem lösen: Wie kann man die Position eines Balles in einem Video mithilfe von Software erkennen? In Python gibt es glücklicherweise eine Bibliothek, die sich mit genau diesem Problem beschäftigt. Die Open Source Computer Vision Bibliothek, kurz OpenCV, stellt mehr als 2.500 Algorythmen zur verfügung, mit denen Bild und Videomaterial analysiert werden kann. Dabei kann diese Bibliothek mit nur einer Zeile in jedes Pythonprojekt geladen werden. Für das "Tracken" eines Balls war für uns vor allen Dingen die Farbfilter-Funktion von OpenCV wichtig. Dabei wird ein Bild oder Video in hinsicht auf die in ihm enthaltenen Farben analysiert. Wenn der Ball also eine andere farbe als der rest des Bildes ist, kann dieser isoliert werden und damit getrackt werden.


## [pygame](https://www.pygame.org/news)
<img width="200" alt="Bildschirmfoto 2022-04-18 um 19 35 09" src="https://user-images.githubusercontent.com/88385813/163342475-1fa20894-75e3-4dda-90f0-42c2d4296f9e.png">


Der letzte Teil unseres Projekts sollte die Simulation des Wurfs und das Anzeigen der Flugbahn in einem Programm werden. Dafür benötigten wir eine weitere Python-Bibliothek. Pygame ermöglicht uns das Anzeigen von einfachen 2D Grafiken und Kollisionserkennung zwischen diesen Objektenund eignet sich daher sehr gut für unsere Simulation. Des weiteren gibt es zu dieser Bibliothek unzählige Tutorials, die den Umgang mit pygame erheblich erleichtern.

## Der Code im Detail
In der [Code-Sektion](https://github.com/ThorgeHamprecht/InformatikProjektNr.2/blob/main/Code.md) ist unser gesamter Code zu finden. Dort sind die allermeisten Zeilen mit kurzen Stichpunkten erklärt. Auf dieser Projektseite werden die wichtigsten Funktionen von Infinity Throw im Detail erklärt.  

### Ball Erkennung mit OpenCV
-----------------------------
Die zentrale Stütze, auf der unser Projekt basiert, ist das Erkennen des Balls in einem Videostream. Wie schon zuvor genannt verwenden wir dafür OpenCV. Der Code stammt dabei aus dem Internet, da OpenCV für Einsteiger sehr kompliziert ist. Wir haben diesen daher als Grundlage für unser Projekt genommen, um ihn anschließend zu erweitern und anzupassen, damit wir Infinity Throw realisieren konnten. Trotzdem ist es wichtig die funktionsweise von OpenCV zu erläutern:

Im Allgemeinen analysiert OpenCV dabei jeden Frame des von der Laptop-Webcam aufgenommenen Videos nach einem recht einfachen Prinzip. Zunächst wird das Bild auf einen zuvor eingestellten Farbbereich untersucht. Alle Punkte, die in dem Bild in dem ausgewähltem Farbbereich liegen, werden durch OpenCV markiert. Anschließend werden die Markierungen auf ihre Größe untersucht.
```python
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
```
Überschreiten sie dabei eine zuvor definierte Mindestgröße, so wird ein Objekt erkannt und das OpenCV kann nun die Koordinaten des Objekts auf dem Bildschirm angeben. Dies geschieht 60-mal die Sekunde mit dem aktuellen Bild, dass die Webcam aufnimmt. Somit kann die Bewegung des Balls verfolgt werden. Schlussendlich wird anhand einer Liste, in der sich alle aufgenommenen Positionen des Balles befinden, eine Linie zum Nachverfolgen der Ballbewegung gezogen.

Für Infinity Throw haben wir dabei mit einem Programm zum Analysieren des Farbbereichs eines Objekts den Farbbereich unseres Balles bestimmt und in den Code eingefügt.
```python
#Farbbereich für den zu trackenden Ball
greenLower = (86, 39, 97)
greenUpper = (179, 228, 255)
```
Auch einen Mindestradius des Objekts haben wir nach einigen Tests auf 7 festgelegt. Mit dieser Größe haben wir die besten Ergebnisse erzielt.
```python
if radius > 7:
			# draw the circle and centroid on the frame,
			# then update the list of tracked points
			cv2.circle(frame, (int(x), int(y)), int(radius),
					   (0, 255, 255), 2)
			cv2.circle(frame, center, 5, (0, 0, 255), -1)
			objektErkannt = True
```
Nur wenn das erkannte Objekt diesen Radius übersteigt wird unsere Messdatenerhebung getstartet. Dies passiert durch den Boolian "objektErkennt", der bei einem Objekt, dass die Mindestgröße übersteigt auf "True" gesetzt wird.

Lässt man nur diesen Teil des Programms laufen, so sieht das Ergebnis folgendermaßen aus:


https://user-images.githubusercontent.com/88385813/163868786-1f89513b-5df0-4d96-a23b-fe141bec16a0.mp4


Das Video der Webcam wird analysiert und die Bewegung des Balles mit einer Linie nachgezeichnet.




### Aufnahme von Messpunkten (Aufnahme von Koordinaten des Balles zu unterschiedlichen Zeitpunkten)
--------------------------------
Für das Modellieren einer Flugbahn des Balles müssen zunächst Messpunkte aus dem Ball-Tracking entnommen werden. Dafür werden drei Messpunkte zu unterschiedlichen Zeitpunkten aufgenommen werden. Dabei musste beachtet werden, dass ein schnelles Aufnehmen der Messpunkte zwar ein schnelleres Vorhersagen der Flugkurve zulässt, aber auch weniger genau ist. Hier haben wir durch Ausprobieren einen guten Mittelwert gefunden. Dabei wird der erste Messpunkt nach 5/60 Sekunden aufgenommen, da der Loop 60-mal die Sekunde läuft und der Messpunkt nach 5 Sekunden aufgenommen wird. Dies geschieht allerdings erst nachdem der Ball überhaupt im Bild erkannt wurde, denn nur dann wird durch das anfängliche if-Statement der Code ausgeführt. Der zweite Messpunkt wird 15/60 Sekunden später aufgenommen und der finale Messpunkt noch einmal 15/60 Sekunden später. Für alle 3 Messpunkte gibt es sowohl eine Variable für die x-Koordinate als auch die y-Koordinate. Zum Zeitpunkt der Aufnahme des jeweiligen Messpunkts wird die aktuelle Position des Balls den Variablen gleichgesetzt. Somit können später in den Berechnungen alle Variablen einzeln und unabhängig voneinander verwendet werden. Zusätzlich werden auch noch die Zeitdifferenzen zwischen den Messpunkten für spätere Berechnungen aufgezeichnet. Abschließend werden die Messpunkte in der Konsole ausgeschreiben, um das "Debugging" bei möglichen fehlern zu erleichtern.
```python
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

```

### Das Errechnen einer Flugkurve nach Aufnahme der Messpunkte
-------------------
Nach einigem herumprobieren mit anderen Methoden schaffte es die Modellierung der Flugkurve des Balls durch ein Lineares-Gleichungssystem schlussendlich in Infinity Throw, da dieses die genauesten Ergebnisse lieferte. Da wir durch die physikalischen Begebenheiten eines schrägen Wurfes wussten, dass wir nach einer Funktion zweiten Grades suchen, ist das zugehörige Gleichungssystem recht leicht aufzustellen und kann mittels einer Bibliothek in Python leicht gelöst werden. Hierbei müssen lediglich die Messwerte und die Quadrate der x-Koordinaten in die Funktion zum Lösen von LGS eingesetzt werden und die Funktion berechnet anschließen die Werte a, b und c nach dem Schema einer Funktion a + b * x + c * x^2.
```python
x1 = 0.2
x1Quadrat = x1**2
y1 = 1.18
x2 = 0.4
x2Quadrat = x2**2
y2 = 1.3
x3 = 0.6
x3Quadrat = x3**2
y3 = 1.38


A = np.array([[1, x1, x1Quadrat], [1, x2, x2Quadrat], [1, x3, x3Quadrat]])
B = np.array([y1, y2, y3])
X = np.linalg.solve(A,B)

a = X[0]
b = X[1]
c = X[2]

y = (a + b * x + c * x**2)
```
Dadurch erhalten wir die Funktion der Flugkurve des Balles, mit der wir später unsere Simulation speisen können.

### Grundfunktionsweise der Simulation mit pygame
----------------------
Auch die Simulation läuft wie das Ball-Tracking in einem Loop, der 60-mal die Sekunde ausgeführt wird. Dabei werden in pygame jeden Frame 2D-Objekte auf den Screen gemalt. Je später man ein Objekt also in dem Loop malt, desto höher ist die Ebene auf der sich das Objekt befindet. Das letzte im Loop gemalte Objekt ist also immer ganz oben über den anderen zu sehen. Die Simulation besteht deshalb aus sehr vielen Hintergrundobjekten, wie z.B. der Meterskalierung oder dem blauen Hintergrund, die jeden Frame an der gleichen Position gemalt werden und eigentlich nur dem Ball, der sich bewegen muss. Daher sind die meisten Zeilen der Simulation ausschließlich für das Platzieren von Hintergrundobjekten:
```python
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

```

### Ballwegegung in der Simulation
----------------
Nachdem das LGS gelöst wurde und somit die Flugkurve des Balls berechnet wurde muss diese in der Simulation umgesetzt werden. Dafür muss der Ball jeden Frame eine gewisse Distanz bewegt werden. Die Geschwindigkeit auf der x-Achse wird dabei durch die zuvor aufgenommenen Zeitdifferenzen zwischen den Messpunkten bestimmt:

```python
# Geschwindigkeit in x richtung berechnen
			xGeschwindigkeit = (x3 - x1)/((t1 + t2) / 2)
```
Dies ist dann eine Geschwindigkeit in x-Einheiten pro Sekunde. Mit dieser Information und der berechneten Flugkurve kann die Position des Balls dann in echtzeit verändert werden, so das die reale Flugkurve in der Simulation nachgestellt wird:
```python
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
```
Außerdem wird die Bewegung des Balls eingefroren, sobald er den Boden berührt, der sich auf der Höhe 740 befindet. Kurze Zeit danach ist das Programm dann bereit für den nächsten Wurf.

### Treffer mit dem Fass registrieren
------------------
Nun wo die Simulation die Flugkurve des Balles richtig darstellt muss nur noch das Fass in das der Ball geworfen werden soll eingefügt werden. Dieses wird mit einer Zufallig generierten x-Position platziert. Die Entfernung zum Fass wird vorher angezeigt und der Spieler kann danach versuchen diese Entfernung zu erreichen und damit das Fass zu treffen.
```python
# Fass Anzeigen
		screen.blit(fassImage,(posFass, zufallX))  # x-Koordinate darf zwischen 0 und 1120 sein, random number generation
		# Fass position ändern
		zähler = zähler + 1
		if zähler == 120:
			zähler = 0
			zufallX = random.uniform(0, 1120)  # Zufällige Zahl zwischen 0 und 1120
```
Um zu erkkennen, ob der Ball das Fass getroffen hat muss eine Kollisionskontrolle eingefügt werden, die die Position des Fasses mit der des Balls vergleicht. Pygame hat dafür eine Funktion um Kollision zwischen Objekten zu überprüfen. Dabei kann überprüft werden, ob sich ein Punkt (der Ball) in einem Rechteck (das Fass) befindet. Trifft der Ball das Fass befindet er sich also in der Rechteckigen-Hitbox des Fasses. Passiert dies, so erhält der Spieler Punkte für den erfolgreichen Wurf.
```python
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

```
