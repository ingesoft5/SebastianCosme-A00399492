// =============================================================================
// Taller Evaluativo 2 - Fase 4: Pipeline Declarativo en Jenkins
//
// Complete los TODOs de cada etapa. Recuerde:
//   - Prohibido el tag ":latest" al publicar en Nexus (empaquetamiento
//     inmutable, Fase 2).[cite: 9]
//   - Las credenciales (usuario/clave de Nexus, token de GitHub) se
//     inyectan mediante los IDs configurados en Jenkins > Credentials,
//     NUNCA en texto plano dentro de este archivo.[cite: 9]
// =============================================================================

pipeline {
    agent any

    environment {
        // La variable se inicializa vacía, se asignará dinámicamente en el script[cite: 9]
        IMAGE_TAG = ""

        // Configuración de repositorios locales apuntando a Nexus[cite: 8, 9]
        NEXUS_REGISTRY   = "localhost:9080"
        NEXUS_MAVEN_REPO = "http://localhost:9081/repository/maven-releases/"

        // ID de la credencial creada en la Fase 3[cite: 8, 9]
        NEXUS_CREDENTIALS_ID = "nexus-credentials"
    }

    stages {

        stage('Checkout & Test') {
            steps {
                checkout scm[cite: 9]

                script {
                    // Esquema de versionado inmutable: número de build + hash corto del commit[cite: 8, 9]
                    env.IMAGE_TAG = "${env.BUILD_NUMBER}-${env.GIT_COMMIT.take(7)}"
                }

                dir('backend') {
                    // Ejecución de pruebas unitarias sobre el backend[cite: 8, 9]
                    sh 'mvn test'
                }
            }
        }

        stage('Package & Tag Inmutable') {
            steps {
                dir('backend') {
                    // Compilación del .jar y construcción de la imagen Docker[cite: 8, 9]
                    sh 'mvn clean package -DskipTests'
                    sh "docker build -t ${env.NEXUS_REGISTRY}/studytrack-backend:${env.IMAGE_TAG} ."
                }
                dir('frontend') {
                    // Construcción de la imagen Docker multi-stage del frontend[cite: 8, 9]
                    sh "docker build -t ${env.NEXUS_REGISTRY}/studytrack-frontend:${env.IMAGE_TAG} ."
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                // Inyección de credenciales seguras para la publicación[cite: 8, 9]
                withCredentials([usernamePassword(credentialsId: "${env.NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    
                    dir('backend') {
                        // Publicación del artefacto .jar en Nexus[cite: 8]
                        sh 'mvn deploy -DskipTests'
                    }
                    
                    // Publicación de las imágenes Docker[cite: 8]
                    sh "docker login ${env.NEXUS_REGISTRY} -u ${NEXUS_USER} -p ${NEXUS_PASS}"
                    sh "docker push ${env.NEXUS_REGISTRY}/studytrack-backend:${env.IMAGE_TAG}"
                    sh "docker push ${env.NEXUS_REGISTRY}/studytrack-frontend:${env.IMAGE_TAG}"
                }
            }
        }

        stage('Deploy & Smoke Test') {
            steps {
                // Despliegue local referenciando la imagen publicada[cite: 8, 9]
                sh 'export IMAGE_TAG=${IMAGE_TAG} && docker compose -f deploy/docker-compose.yml up -d'
                
                // Validación con reintentos para asegurar que la API levanta correctamente[cite: 8]
                sh 'curl --retry 10 --retry-delay 5 --retry-connrefused -f http://localhost:8080/api/tasks'
            }
        }
    }

    post {
        success {
            echo "Pipeline finalizado en verde. Artefactos publicados con tag: ${env.IMAGE_TAG}"[cite: 9]
        }
        failure {
            echo "El pipeline falló. Revise los logs de la etapa correspondiente antes de reintentar."[cite: 9]
        }
    }
}