# Übung 5

**Hinweise:**

Zunächst einmal können Sie dieses Repository wieder über git klonen.  

**Aufgabe 1 - Eine Wordpress und MySQL App mit Docker Compose aufsetzen**

In dieser Übung werden wir Docker Compose verwenden, um ein kleines Container-Cluster bestehend aus einer Wordpress-Instanz und einer MySQL-Instanz aufzusetzen. Hier finden Sie weitergehende Informationen zu Docker Compose: [https://docs.docker.com/compose/](https://docs.docker.com/compose/)  

Legen Sie zunächst einen Ordner für diese Aufgabe an (z. B. Aufgabe1).  Legen Sie dort die folgende Docker Compose Konfigurationsdatei ab:
   ```bash
docker-compose.yml
   ```
Starten Sie die App mit:
   ```bash
docker compose up
   ```
Prüfen Sie die beiden laufenden Container einmal im Docker Desktop Dashboard.  

Öffnen Sie ihren Browser mit der URL:
   ```bash
http://localhost:8080
   ```
Sie sollten nun die Wordpress-Startseite sehen.  

Stoppen Sie danach die Container wieder.

**Aufgabe 2 - Ein erstes Kubernetes Cluster aufsetzen**

Installieren Sie [kubectl](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/)

Überprüfen Sie über die Kommandozeile, ob kubectl erfolgreich installiert wurde mittels
   ```bash
kubectl version
   ```
Sie können nun ein einfaches Test-Cluster mit einem einfachen Server starten:

   ```bash
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080

   ```
Lassen Sie sich die laufenden Deployments anzeigen mit:
   ```bash
kubectl get deployments

   ```
Lassen Sie sich die laufenden Pods anzeigen mit:
   ```bash
kubectl get pods

   ```
Öffnen Sie das Docker Desktop Dashboard und stoppen Sie dort den laufenden Pod bzw. Container. Was passiert?

Lassen Sie sich in der Kommandozeile die Cluster Events anzeigen:
   ```bash
kubectl get events

   ```
Öffnen Sie ihren Webbrowser mit 
   ```bash
http://localhost:8080
   ```
Was ist zu sehen?

Fügen Sie dem Cluster einen Load Balancer Service hinzu, der den Dienst nach außen hin verfügbar macht:
   ```bash
kubectl expose deployment hello-node --type=LoadBalancer --port=8080

   ```
Prüfen Sie, ob der Service läuft mit:
   ```bash
kubectl get services

   ```
Probieren Sie nun noch einmal den Aufruf über ihren Webbrowser.

Löschen Sie die Ressourcen anschließend wieder mit:
   ```bash
kubectl delete service hello-node

   ```
   ```bash
kubectl delete deployment hello-node

   ```
**Aufgabe 3 - Ein Kubernetes Cluster mit mehreren Pods starten**

Mit der Konfigurationsdatei 
   ```bash
nginx-deployment.yaml

   ```
können Sie ein einfaches Kubernetes Cluster starten, in dem ein nginx Server in einem Pod läuft und über den NodePort Service von außen verfügbar macht.  

Starten Sie das Cluster einmal mit
   ```bash
kubectl apply -f nginx-deployment.yaml

   ```
Sie sollten den Dienst in ihrem Browser dann erreichen können über:
   ```bash
http://localhost:30000

   ```
Löschen Sie die Ressourcen anschließend wieder.  

Ändern Sie die Konfigurationsdatei so, dass jeweils 3 Pods mit Servern gestartet werden. Was passiert, wenn Sie die Container stoppen?

**Aufgabe 4 - Ressource Limits**

Passen Sie die Konfigurationsdatei aus Aufgabe 3 zunächst wieder so an, dass nur ein Pod gestartet wird.  

Ändern Sie die Konfigurationsdatei nun so, dass für die CPU- und Speicher-Nutzung des Containers folgende Mindest- und Höchstgrenzen gesetzt werden:

- Untergrenzen: CPU 250m / RAM 64Mi
- Obergrenzen: CPU 500m / RAM 128Mi

Was bedeuten die Angaben?  

Starten Sie das Cluster und schauen Sie sich unter den "Stats" im Docker Desktop Dashboard die CPU- und RAM-Nutzung an.  

Rufen Sie den Server über ihren Webbrowser auf und schauen Sie sich die Änderungen in der Auslastung in "Stats" an.

Was würde passieren, wenn die Obergrenzen für CPU und RAM überschritten werden?  

Finden Sie heraus, welches die kleinstmöglichen Grenzen für CPU und Speicher sind. Passen Sie die Konfigurationsdatei entsprechend an.   

Starten Sie das Cluster erneut und prüfen Sie im Docker Desktop Dashboard den Status des Containers.  

Experimentieren Sie mit anderen Werten für die Ressource Limits und prüfen Sie den Status des Containers.

**Aufgabe 5 - Automatisches Skalieren mit den Horizontal Pod Autoscaler (HPA)**

Setzen Sie die Ressource Limits zunächst wieder so, wie zu Beginn von Aufgabe 4.  

Konfigurieren Sie nun einen Horizontal Pod Autoscaler (HPA), der bei überschreiten von 80% der CPU-Auslastung einen weiteren Pod startet.

Rufen Sie den Server über ihren Webbrowser auf und schauen Sie sich die Änderungen in der Auslastung in "Stats" an.

Installieren Sie sich das Programm [GNU Parallels](https://www.gnu.org/software/parallel/)  

Hinweis: Sie können das Programm über Homebrew einfach installieren über:
   ```bash
brew install parallel

   ```

Über folgenden Befehl können Sie eine Sequenz von 1000 parallelen Request (jeweils 100) an den Server schicken:
   ```bash
seq 1 1000 | parallel -j 100 curl -s http://localhost:30000/

   ```
Schauen Sie sich nun die Änderungen in der Auslastung in "Stats" an.  

Experimentieren Sie mit den Ressource Limits und der Anzahl und dem Umfang der parallelen Request so, dass man die Aktivitäten des HPA im Dashboard verfolgen kann.







# Übung 4

**Hinweise:**

In den Aufgaben 2 bis 6 dieser Übung verwenden wir Docker um Anwendungen zu containerisieren und zu nutzen. Hierzu können Sie Docker Desktop installieren: [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/). Hier finden Sie eine Übersicht der verschiedenen Docker-Befehle: [https://docs.docker.com/reference/cli/docker/](https://docs.docker.com/reference/cli/docker/)  

Anmerkung: Starten Sie Docker einmal und gehen Sie in die Einstellungen. Hier können Sie schon mal den Haken bei "Enable Kubernetes" setzen (das brauchen wir zwar jetzt noch nicht, aber zu einem späteren Zeitpunkt).

Jede der folgenden Aufgaben sollte in einem eigenen Projekt realisiert werden.   

Zunächst einmal können Sie das Repository zu Übung 4 über git klonen.  

In diesem Repository finden sie Dockerfiles, die nach folgendem Schema aufgebaut sind:

   ```bash
Dockerfile_n
   ```
Das n ist jeweils eine Ziffer (1,2,3, ...), welche die zugehörige Aufgabe referenziert. Um die Datei in dem jeweiligen Projekt/Repository nutzen zu können, müssen Sie sie folgendermaßen umbenennen:

   ```bash
Dockerfile
   ```

**Aufgabe 1 - Eine Python-App in der Google App Engine realisieren**

Hinweis: Diese Übung ist freiwillig! Sie erfordert zumindest das Anlegen eines kostenlosen Testaccounts unter https://cloud.google.com/. Selbst der kostenlose Test-Account erfordert jedoch die Angabe von z. B. Kreditkarteninformationen. Es liegt in ihrem eigenen Ermessen, ob Sie entsprechende Angaben machen möchten. Wichtig ist in jedem Fall, die angelegten Ressourcen wieder zu löschen (wie, das wird in dieser Anleitung erklärt). Die Verwendung der entsprechenden Dienste erfolgt jedoch unter eigener Verantwortung!

Anmerkung: Wir nutzen hier ein Angebot der Google Cloud exemplarisch. Dies lässt sich bei anderen Cloud-Providern wie AWS, Azure, IBM etc. in ähnlicher Weise nutzen.

Unter folgendem Link finden Sie ein Tutorial, um eine einfache Python-App in der Google App Engine zu realisieren: [https://codelabs.developers.google.com/codelabs/cloud-app-engine-python3#0](https://codelabs.developers.google.com/codelabs/cloud-app-engine-python3#0) 

Anmerkung: Im Tutorial fehlt leider der Hinweis, dass die App Engine Administratorrechte wie untenstehend aktiviert sein müssen. Aktivieren Sie sie zunächst und folgen Sie dann den Anweisungen im Tutorial.

![app_eng_admin.png](app_eng_admin.png)

**Wichtig:**
Vergessen Sie nicht am Ende des Tutorials die Ressourcen wieder zu bereinigen, die Sie angelegt haben:
![ress_ber.png](ress_ber.png)
![del_ress.png](del_ress.png)



**Aufgabe 2 - Eine Python-App mit Docker containerisieren**

Klonen Sie das Repository zu dieser Übung.  

Erstellen Sie einen Ordner mit folgenden Dateien:

- app.py
- requirements.txt
- Dockerfile (zur Aufgabe 2, siehe Umbenennung oben)

Starten Sie Docker.

Erzeugen Sie ein Docker-Image:

   ```bash
docker build -t wikisearch .
   ```

Starten Sie den Container:
   ```bash
docker run -p 8888:80 wikisearch
   ```
Rufen Sie in ihrem Browser folgende Adresse auf:
   ```bash
http://localhost:8888?wiki=Nürnberg
   ```
Sie sollten nun eine kurze Zusammenfassung zur Stadt Nürnberg sehen.  

Probieren Sie das auch mit anderen Städtenamen aus.  

Prüfen Sie im Docker-Desktop-Dashboard ob der Container läuft.

Prüfen Sie über die Kommandozeile, ob der Container läuft:

   ```bash
docker ps -a

   ```
Quizfrage: Wie vergibt Docker die Default-Namen für seine Container?

Stoppen Sie den Container über das Dashboard oder über die Kommandozeile mit:
   ```bash
docker stop [container_name]

   ```
Finden Sie heraus, wie man einem Container einen selbstgewählten Namen geben kann und starten Sie ihn noch einmal mit einem selbst gewählten Namen.  

Stoppen Sie den Container wie oben angegeben.


**Aufgabe 3 - Eine Java-App mit Docker containerisieren**

Stellen  Sie sicher, dass Sie ein Java Development Kit (JDK) installiert haben [https://www.oracle.com/de/java/technologies/downloads/](https://www.oracle.com/de/java/technologies/downloads/).

Erstellen Sie einen Ordner mit folgenden Dateien:

- HelloWorld.java
- Dockerfile (zur Aufgabe 3, siehe Umbenennung oben)

Erzeugen Sie ein Docker-Image:
   ```bash
docker build -t hello-world-java .
   ```
Starten Sie den Container mit:
   ```bash
docker run hello-world-java

   ```
Prüfen Sie ob der Container läuft. Was ist der Unterschied zu Aufgabe 2?

**Aufgabe 4 - Eine eigene App mit Docker containerisieren und ausführen**

Schreiben Sie ein Programm ihrer Wahl (und ein zugehöriges Dockerfile), dass Sie containerisieren und ausführen.  


**Aufgabe 5 - Den Nginx-Webserver als Docker Image nutzen**

Legen Sie sich einen Account auf [Docker Hub](https://hub.docker.com/) an.  

Ziehen Sie sich das Image des nginx-Webservers:
   ```bash
docker pull nginx
   ```
Starten Sie den Webserver mit:
   ```bash
docker run --name some-nginx -d -p 8080:80 nginx
   ```
Öffnen Sie ihren Browser. Unter folgender Adresse sollten Sie die Startseite des Webservers sehen:
   ```bash
http://localhost:8080
   ```
Stoppen Sie den Container.  

Folgendermaßen können Sie sich die vorhandenen Docker-Images mit ihrer zugehörigen ID anzeigen lassen:
   ```bash
docker images
   ```
Löschen Sie das Image des nginx-Servers wieder mit:
   ```bash
docker rmi [Image-ID]
   ```
bzw. mit
   ```bash
docker rmi -f [Image-ID]
   ```

**Aufgabe 6 - Ein eigenes Docker-Image auf Docker Hub pushen und pullen**

Erstellen Sie auf Docker Hub ein Repository, auf das Sie das Image pushen können:
![dockerhub_repo.png](dockerhub_repo.png)

Öffnen Sie ein Terminal und lassen Sie sich ggf. ihre lokal vorhandenen Docker-Images anzeigen. Das Image, das sie pushen möchten, müssen sie zunächst wie im folgenden Beispiel taggen:
   ```bash
docker tag local-image-id yourusername/myrepo:latest
   ```
Hierbei ist local-image-id die ID ihres lokalen Docker-Images, yourusername ihr Benutzername und myrepo der Name des Repositories, das Sie angelegt haben.  

Loggen Sie sich nun im Terminal ein mit:
   ```bash
docker login
   ```

Jetzt können Sie das Image pushen mit:
   ```bash
docker push yourusername/myrepo:latest
   ```
Überprüfen Sie, ob das Image hochgeladen wurde.  

Anschließend können Sie das Image pullen mit:
   ```bash
docker pull yourusername/myrepo:latest
   ```
Und so können Sie den Container dann starten:
   ```bash
docker run yourusername/myrepo:latest
   ```

