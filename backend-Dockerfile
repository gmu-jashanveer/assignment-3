# Start with a base image that has JDK
FROM openjdk:17-jdk-slim

# Copy the Spring Boot jar to the container
ARG JAR_FILE=./target/swe645-0.0.1-SNAPSHOT.jar
COPY ./target/swe645-0.0.1-SNAPSHOT.jar app.jar

# Expose the application port (default is 8080 for Spring Boot)
EXPOSE 9090

# Set the entry point to run the Spring Boot app
ENTRYPOINT ["java", "-jar", "/app.jar"]