FROM maven:3.8.1-jdk-8-slim

WORKDIR /app

COPY . .

RUN mvn package -DskipTests
RUN mv target/worker-jar-with-dependencies.jar /run/worker.jar

CMD java -jar /run/worker.jar