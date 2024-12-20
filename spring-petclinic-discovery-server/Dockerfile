FROM maven:3.8.4-openjdk-11 AS build
WORKDIR /app
COPY . .
WORKDIR /app/spring-petclinic-discovery-server
RUN mvn clean install -DskipTests

FROM openjdk:11-jre
WORKDIR /app
COPY --from=build /app/spring-petclinic-discovery-server/target/*.jar app.jar
CMD ["java", "-jar", "app.jar"]
