autoscale: true
# Spring Boot Docker Workshop
### Alexander Sparkowsky, 02.05.2018

---

# Worum geht's?

## Wir packen eine einfache Spring Boot Anwendung in einen Container und haben dabei Spass 🤓

---

# Wie?

* Eine kleine Einführung in Docker
* Erste Annäherung auf der Kommandozeile
* Erstellen einer einfachen Spring Boot 2 Anwendung
* Betreiben der Anwendung in einem Container


---

# Bevor wir loslegen...

## 🧐 Hat jeder Docker installiert?

### [https://www.docker.com/community-edition](https://www.docker.com/community-edition)

### [https://bit.ly/dws201805](https://bit.ly/dws201805)

---

# Virtual Machine vs. Docker

Virtual Machine | Docker
---|---
Vollständiges OS | Nur so viel OS wie notwendig
Mehrere Prozesse | Ein isolierter Prozess pro Container
Umfangreiche Resourcen | Leichtgewichtig
Boot-Zeit | Schnell gestartet (und gestoppt)

---

# Die Technologien hinter Docker

* Control Groups
  * Resourcen einer Anwendung einschränken
* Union filesystem
  * Schichten, leichtgewichtig, unveränderbar
* 1 Container = 1 Prozess
* Namespaces
  * Isolierter Arbeitsbereich für Prozess, Netwerk, Mount etc.

---

# Die Architektur von Docker

* Docker Host: **`dockerd`**
  * Mac und Windows: Eine virtuelle Maschine
* Client: **`docker`**
* Registry

---

Images | Containers
---|---
Read-only<br/><br/>Vorlage<br/><br/>Baut auf anderen Images auf<br/><br/>Selbst erstellen oder Fertige benutzen | Ausführbare Instanz eines Images<br/><br/>Verwalten über Docker Kommando<br/><br/>Ausführung durch den Docker Daemon<br/><br/>Isoliert vom Host-System<br/><br/>Zustandsbehaftet

---

# Los geht's 🐳

---

# Hello-World mit Docker

`$ docker container run hello-world`

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
...
```

---

# Mal schnell eine Shell unter Ubuntu starten

`$ docker container run --rm -it ubuntu /bin/bash`

1. Ubuntu Image von `hub.docker.com` laden
1. Container erzeugen
1. Read/Write Filesystem erzeugen
1. Netzwerk-Interface erzeugen
1. Container (im Vordergrund) starten
1. `/bin/bash` ausführen
1. Terminal verbinden (`-it`)
1. Nach dem Verlassen: Container stoppen und löschen (`--rm`)

---

# Wir starten einen Webserver

`$ docker container run -d -p 80:80 nginx`

* Ausführen im Hintergrund (`-d`)
* Port `80` mit dem Host verbinden (`-p 80:80`)

`$ docker ps`

* Anzeigen der laufenden Container

`$ docker container stop <containerId>`

* Container stoppen

---

# Dockerfile

```

FROM scratch
COPY hello /
CMD ["/hello"]
```

* Definiert den Aufbau eines Images
* Benennt das Basis-Image
* Installieren und Konfigurieren:
  * Softwarepakete
  * Applikation
  * Benutzer
  * ...

---

1) Wir initialisieren eine Spring Boot Anwendung

```
curl https://start.spring.io/starter.tgz \
  -d dependencies=web,actuator,devtools \
  -d language=kotlin \
  -d type=gradle-project \
  -d baseDir=spring-docker-workshop \
  -d groupId=de.roamingthings.example \
  -d artifactId=docker-workshop \
  -d name=docker-workshop \
  | tar -xzvf -
```

---

2) Wir bauen die Anwendung

```
$ cd spring-docker-workshop
$ ./gradlew build
```

---

3) Wir erstellen ein Dockerfile

```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG JAR_FILE
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

---

4) Wir erstellen das Image

```
docker image build --build-arg JAR_FILE=build/libs/spring-docker-workshop-0.0.1-SNAPSHOT.jar -t spring-docker-workshop .
```

5) Wir erzeugen und starten einen Container

```
docker container run --rm -d -p 8080:8080 --name spring-docker-workshop spring-docker-workshop
```

6) Wir öffnen eine Shell im Container

```
docker container exec -it spring-docker-workshop /bin/bash
```

7) Container anhalten

```
docker container stop workshop
```

---

# Und nun mit Gradle

Projekt zum Auschecken:

```
$ git clone \
https://github.com/roamingthings/spring-docker-workshop
$ cd spring-docker-workshop
```

Projekt bauen und Container starten:

```
$ ./gradlew startContainer
```

Health-Endpunkt aufrufen

```
$ curl http://localhost:8080/actuator/health
{"status":"UP"}
```

---

# Container Prozess und Healthchecks

```
$ docker container ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                        PORTS                    NAMES
e633ad6303d9        cc17f0653fae        "java -Djava.securit…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:8080->8080/tcp   spring-docker-workshop
```

```
$ docker container logs -f spring-docker-workshop
2018-05-02 14:25:40 DEBUG 1 --- Looking up handler method for path /actuator/health
...
2018-05-02 14:25:45 DEBUG 1 --- Looking up handler method for path /actuator/health
...
2018-05-02 14:25:50 DEBUG 1 --- Looking up handler method for path /actuator/health
...
```

---

# IDE time

---

# Vielen Dank!

## `https://github.com/roamingthings/spring-docker-workshop`
