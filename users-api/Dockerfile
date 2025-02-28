# Build stage
FROM maven:3.8-openjdk-8 AS builder

# Set the working directory
WORKDIR /app

# Copy pom.xml first to leverage Docker caching
COPY pom.xml .

# Download dependencies only (avoids redownloading on every build)
RUN mvn dependency:go-offline -B

# Copy the entire source code
COPY src ./src

# Build the application (skip tests for faster builds)
RUN mvn clean package -DskipTests

# Final stage: Use a minimal JRE image
FROM openjdk:8-jre-slim

# Set the working directory
WORKDIR /app

# Copy the built JAR from the builder stage
COPY --from=builder /app/target/*.jar app.jar

# Expose the required port
EXPOSE 8083

# Set environment variables (optional: these are better set in a Kubernetes/Compose config)
ENV SERVER_PORT=8083 \
    JWT_SECRET=${JWT_SECRET}

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]