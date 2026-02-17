FROM alpine/java:17-jdk
ARG JAR_FILE=target/demo-0.0.2-SNAPSHOT.jar
ADD ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
