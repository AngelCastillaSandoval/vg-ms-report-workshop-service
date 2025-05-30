pipeline {
    agent any
    
    environment {
        SONARQUBE = 'SonarCloud'  // Nombre del servidor SonarQube configurado en Jenkins
        SONAR_TOKEN = credentials('SONAR_TOKEN')  // Token de SonarCloud como credencial
        PROJECT_KEY = 'AngelCastillaSandoval_vg-ms-report-workshop-service'  // Nombre corregido del proyecto en SonarCloud
        SONAR_ORGANIZATION = 'angelcastillasandoval'  // Organización en SonarCloud
    }
    
    tools {
        maven 'Maven 3.8.1'  // Maven configurado en Jenkins
        jdk 'JDK 17'  // JDK 17 configurado en Jenkins
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    // Clonar el repositorio y cambiar a la rama 'main'
                    git branch: 'main', url: 'https://github.com/AngelCastillaSandoval/vg-ms-report-workshop-service.git'
                }
            }
        }
        
        stage('Compile with Maven') {
            steps {
                script {
                    sh 'mvn clean compile'
                }
            }
        }
        
      stage('Run Unit Tests') {
    steps {
        script {
            sh '''
                mvn test
            '''
        }
    }
    post {
        always {
            // Publicar resultados de pruebas
            junit testResults: 'target/surefire-reports/*.xml'
        }
    }
}
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv("${env.SONARQUBE}") {
                        sh '''
                            mvn sonar:sonar \
                                -Dsonar.projectKey=${PROJECT_KEY} \
                                -Dsonar.organization=${SONAR_ORGANIZATION} \
                                -Dsonar.host.url=https://sonarcloud.io \
                                -Dsonar.login=${SONAR_TOKEN} \
                                -Dsonar.projectName=vg-ms-report-workshop-service \
                                -Dsonar.qualitygate.wait=true \
                                -Dsonar.scanner.force=true \
                                -Dsonar.scm.disabled=true \
                                -Dsonar.scm.provider=git \
                                -Dsonar.analysis.mode=publish
                        '''
                    }
                }
            }
        }
        
        stage('Generate .jar Artifact') {
            steps {
                script {
                    sh 'mvn package -DskipTests'
                }
            }
            post {
                always {
                    // Archivar el JAR generado
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
    
    post {
        always {
            script {
                // Limpiar workspace después de cada build
                cleanWs()
            }
        }
        success {
            echo 'Build completado exitosamente!'
            echo 'Análisis de SonarQube pasado!'
        }
        failure {
            echo 'Build fallido!'
        }
        unstable {
            echo 'Build inestable!'
        }
    }
}