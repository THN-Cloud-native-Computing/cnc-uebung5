# Übung 5 - Cluster Orchestrierung

![kubernetes.png](kubernetes.png)

**Hinweise:**

Zunächst einmal können Sie dieses Repository wieder über git klonen.  

Legen Sie für jede der Aufgaben einen Ordner an, in dem Sie die entsprechenden Konfigurationsdateien ablegen.

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

**Aufgabe 4 - Resource Limits**

Passen Sie die Konfigurationsdatei aus Aufgabe 3 zunächst wieder so an, dass nur ein Pod gestartet wird.  

Ändern Sie die Konfigurationsdatei nun so, dass für die CPU- und Speicher-Nutzung des Containers folgende Mindest- und Höchstgrenzen gesetzt werden:

- Untergrenzen: CPU 250m / RAM 64Mi
- Obergrenzen: CPU 500m / RAM 128Mi

Was bedeuten die Angaben?  

- Starten Sie das Cluster und schauen Sie sich unter den "Stats" im Docker Desktop Dashboard die CPU- und RAM-Nutzung an.  

- Rufen Sie den Server über ihren Webbrowser auf und schauen Sie sich die Änderungen in der Auslastung in "Stats" an.

- Was würde passieren, wenn die Obergrenzen für CPU und RAM überschritten werden?  

- Finden Sie heraus, welches die kleinstmöglichen Grenzen für CPU und Speicher sind. Passen Sie die Konfigurationsdatei entsprechend an.   

- Starten Sie das Cluster erneut und prüfen Sie im Docker Desktop Dashboard den Status des Containers.  

- Experimentieren Sie mit anderen Werten für die Ressource Limits und prüfen Sie den Status des Containers.

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

![cpu-usage.png](cpu-usage.png)

**Aufgabe 6 - Health Checks**

Nehmen Sie die ursprünglich Konfigurationsdatei zu Aufgabe 3 als Ausgangspunkt.  Die Konfiguration soll nun um Health Checks erweitert werden.  

Was bedeuten in diesem Zusammenhang die Begriffe Liveness Probe und Readiness Probe?  

Passen Sie die Konfigurationsdatei nun so an, dass folgende Health Checks ausgeführt werden:

- Liveness Probe initial nach 15 Sekunden, danach alle 20 Sekunden
- Readiness Probe initial nach 10 Sekunden, danach alle 10 Sekunden

Prüfen Sie im Docker Desktop Dashboard unter "Logs" die Ausführung der Health Checks.

 
