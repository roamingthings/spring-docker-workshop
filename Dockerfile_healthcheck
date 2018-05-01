FROM openjdk:10-slim

MAINTAINER Alexander Sparkowsky <alexander.sparkowsky@epost-dev.de>

RUN apt-get update \
    && apt-get install -y curl

VOLUME /tmp
ARG JAR_FILE
COPY ${JAR_FILE} app.jar

HEALTHCHECK --interval=5s --timeout=3s \
              CMD curl -f http://localhost:8080/actuator/health || exit 1

EXPOSE 8080

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]