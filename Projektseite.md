# Projektseite - infinitythrow


https://user-images.githubusercontent.com/88385813/163343886-85badb2c-bd0d-41ea-952f-dd24a47acf5e.mp4


infinitythrow ist ein Augmented Reality Spiel, das einen echten Ballwurf mit dem Ballwurf in einer Simulation verbindet. Grundsätzlich ist es dabei Ziel, den Ball in eine nur in der Simulation vorhandene Tonne zu werfen. Der Ball wird dabei in der realen Welt geworfen und anschließend ein dementsprechender Klon in der Simulation erstellt, der die selben Eigenschaften, also Geschwindigkeit und Bewegungsrichtung wie der echte Ball hat. Dabei gibt es zentral zwei Teile des Programmcodes: Zuerst die Erkennung der Flugkurve des Balls und anschließend die weitere Berechnung derselben in der Simulation und das Erkennen, ob der Ball die Tone getroffen hat.
## [Python](https://www.python.org/)
![IMG_0880](https://user-images.githubusercontent.com/88385813/157444258-77318303-ed05-49b7-8ace-10eebca0f0f5.png)

Für die Umsetzung unseres Projektes haben wir Python genutzt. Wir haben uns für python entschieden, da Python sich vor allen Dingen durch seine leichte Syntax und vielseitigen Anwendungsgebiete auszeichnet. Dabei kann das Grundgerüst von Python durch viele andere Bibliotheken erweitert und angepasst werden. Da wir zu Anfang unseres projektes noch keine genaue Vorstellung von dem Endprodukt unseres Projekts hatten, stellte Python genau das richtige Grundgerüst dar, um unser Programm später nach unseren Vorstellungen zu erweitern. Des weiteren ist Python auch sehr ähnlich zu C#, eine Programmiersprache mit der wir schon einmal in der Projektfindungsphase des ersten projekts Erfahrungen gemacht hatten.

## [PyCharm](https://www.jetbrains.com/pycharm/)
![pycharm-logo](https://user-images.githubusercontent.com/88385813/163342121-e0e1be82-a348-4d29-a16c-04bcb0c8c442.png)


Für das Schreiben und Ausführen des Codes haben wir uns für für PyCharm entschieden. PyCharm ist relativ ähnlich zu Microsoft Visual Studio. Allerdings ist PyCharm für Python Entwicklung optimiert. In PyCharm kann der Code sowohl geschrieben, als auch ausgeführt werden und das Programm überprüft den Code auch auf Fehler, was das "Debugging" enorm erleichtert. PyCharm wird auch im profressionellem Bereich genutzt ist allerdings für Non-Profit-Projekte kostenlos verfügbar. 

## [OpenCV](https://opencv.org/)
![IMG_0879](https://user-images.githubusercontent.com/88385813/157444014-ec4f9bcf-7afb-4fd1-bd95-0dfce1b4254e.png)

Für die Umsetzung von infinitythrow mussten wir vor allen anderen Dingen ein großes Problem lösen: Wie kann man die Position eines Balles in einem Video mithilfe von Software erkennen? In Python gibt es glücklicherweise eine Bibliothek, die sich mit genau diesem Problem beschäftigt. Die Open Source Computer Vision Bibliothek, kurz OpenCV, stellt mehr als 2.500 Algorythmen zur verfügung, mit denen Bild und Videomaterial analysiert werden kann. Dabei kann diese Bibliothek mit nur einer Zeile in jedes Pythonprojekt geladen werden. Für das "Tracken" eines Balls war für uns vor allen Dingen die Farbfilter-Funktion von OpenCV wichtig. Dabei wird ein Bild oder Video in hinsicht auf die in ihm enthaltenen Farben analysiert. Wenn der Ball also eine andere farbe als der rest des Bildes ist, kann dieser isoliert werden und damit getrackt werden.


## Simulation/Spiel mit [pygame](https://www.pygame.org/news)
![R](https://user-images.githubusercontent.com/88385813/163342475-1fa20894-75e3-4dda-90f0-42c2d4296f9e.png)

Der letzte Teil unseres Projekts sollte die Simulation des Wurfs und das Anzeigen der Flugbahn in einem Programm werden. Dafür benötigten wir eine weitere Python-Bibliothek. Pygame ermöglicht uns das Anzeigen von einfachen 2D Grafiken und Kollisionserkennung zwischen diesen Objektenund eignet sich daher sehr gut für unsere Simulation. Des weiteren gibt es zu dieser Bibliothek unzählige Tutorials, die den Umgang mit pygame erheblich erleichtern.

## Der Code im Detail

### Ball Erkennung mit OpenCV
