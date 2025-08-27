# Step 1: Use an official Gradle image to build the application
FROM gradle:8.3.0-jdk17 AS build

# Set the working directory inside the container
WORKDIR /app

# Copy the entire project to the container
COPY . .

# Build the application
RUN gradle clean bootJar

# Step 2: Use a lightweight JDK image for running the app
FROM openjdk:17-jdk-slim

# Set the working directory for the runtime container
WORKDIR /app

# Copy the JAR file from the build stage
COPY --from=build /app/build/libs/*.jar app.jar

# Download the OpenTelemetry Java agent
ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar /agent/opentelemetry-javaagent.jar

# Set required environment variables. In this case, I am connecting to my Alloy instance (deployed separately from the main app) via OTLP endpoint.
ENV OTEL_SERVICE_NAME=airlines
ENV OTEL_EXPORTER_OTLP_ENDPOINT=http://alloy:4317   
ENV OTEL_EXPORTER_OTLP_PROTOCOL=grpc
ENV OTEL_RESOURCE_ATTRIBUTES=deployment.environment=staging,service.namespace=flight-simulator,service.version=1.0

# Expose the port the application runs on
EXPOSE 8080

# Run the app with the agent
ENTRYPOINT ["java", "-javaagent:/agent/opentelemetry-javaagent.jar", "-jar", "app.jar"]
