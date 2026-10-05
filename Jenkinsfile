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

    tools {
        maven 'Maven3'
    }

    environment {
        NEXUS_REGISTRY   = "localhost:9080"
        NEXUS_MAVEN_REPO = "http://localhost:9081/repository/maven-releases/"
        NEXUS_CREDENTIALS_ID = "nexus-credentials"
    }

    stages {
        stage('Checkout & Test') {
            steps {
                checkout scm

                script {
                 def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                 env.IMAGE_TAG = "${env.BUILD_NUMBER}-${commitHash}"
                 echo "Tag generado para esta ejecucion: ${env.IMAGE_TAG}"
                }

                dir('codigo_base/backend') {
                    sh 'mvn test'
                }
            }
        }

        stage('Package & Tag Inmutable') {
            steps {
                dir('codigo_base/backend') {
                    sh 'mvn clean package -DskipTests'
                    sh "docker build -t ${env.NEXUS_REGISTRY}/studytrack-backend:${env.IMAGE_TAG} ."
                }
                dir('codigo_base/frontend') {
                    sh "docker build -t ${env.NEXUS_REGISTRY}/studytrack-frontend:${env.IMAGE_TAG} ."
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${env.NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    dir('codigo_base/backend') {
                        sh '''
                        echo "<settings><servers><server><id>nexus</id><username>${NEXUS_USER}</username><password>${NEXUS_PASS}</password></server></servers></settings>" > settings.xml
                        mvn deploy -s settings.xml -DskipTests
                        '''
                    }
                    sh "docker login ${env.NEXUS_REGISTRY} -u ${NEXUS_USER} -p ${NEXUS_PASS}"
                    sh "docker push ${env.NEXUS_REGISTRY}/studytrack-backend:${env.IMAGE_TAG}"
                    sh "docker push ${env.NEXUS_REGISTRY}/studytrack-frontend:${env.IMAGE_TAG}"
                }
            }
        }

        stage('Deploy & Smoke Test') {
            steps {
                sh 'export IMAGE_TAG=${IMAGE_TAG} && docker compose -f codigo_base/deploy/docker-compose.yml up -d'
                sh 'curl --retry 10 --retry-delay 5 --retry-connrefused -f http://localhost:8080/api/tasks'
            }
        }
    }

    post {
        success {
            echo "Pipeline finalizado en verde. Artefactos publicados con tag: ${env.IMAGE_TAG}"
        }
        failure {
            echo "El pipeline falló. Revise los logs de la etapa correspondiente antes de reintentar."
        }
    }
}