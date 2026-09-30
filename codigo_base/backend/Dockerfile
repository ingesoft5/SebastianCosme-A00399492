# =============================================================================
# Taller Evaluativo 2 - Fase 2: Empaquetamiento Inmutable Multi-Stage
# Backend: studytrack-api
#
# Complete los TODOs siguiendo las buenas prácticas vistas en el curso:
# reducción de peso, cache de dependencias, usuario no root y ausencia
# total del SDK/compilador en la imagen final de runtime.
# =============================================================================

# Etapa 1: Construccion (Builder)
FROM maven:3.9.6-alpine AS builder
WORKDIR /build
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Etapa 2: Runtime limpio de produccion
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /build/target/*.jar app.jar
EXPOSE 8080
USER 1001
ENTRYPOINT ["java", "-jar", "app.jar"]