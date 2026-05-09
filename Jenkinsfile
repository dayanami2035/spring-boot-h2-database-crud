pipeline {

    agent any

    environment {
        SONAR_SERVER = 'sonarqube-server'
        // EXTRAEMOS DINÁMICAMENTE EL NOMBRE DEL REPO
        // Esto evita que tengas que cambiar el nombre manualmente en cada proyecto
        REPO_NAME = "${env.GIT_URL.split('/').last().split('\\.').first()}"
    }

    tools {
        maven 'maven-default'
    }

    stages {

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Parallel') {

            parallel {

                stage('Build') {
                    steps {
                        sh 'mvn package -DskipTests'
                    }
                }

                stage('Análisis SonarQube') {
                    steps {
                        withSonarQubeEnv("${env.SONAR_SERVER}") {
                            sh """
                                mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar \
                                -Dsonar.organization=dmtorrico \
                                -Dsonar.projectKey=dmtorrico_spring-boot-h2-database-crud \
                                -Dsonar.projectName=spring-boot-h2-database-crud
                            """
                        }
                    }
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }
    }

    post {

        success {
            echo 'Pipeline ejecutado correctamente'
        }

        failure {
            echo 'Pipeline falló'
        }
    }
}