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

                stage('SonarCloud Analysis') {

                    steps {
                        // 'sonarqube_token' debe ser el ID de tu credencial en Jenkins
                        withCredentials([string(credentialsId: 'sonar_token', variable: 'SONAR_TOKEN')]) {

                            withSonarQubeEnv("${env.SONAR_SERVER}") {
                                    sh """
                                        mvn sonar:sonar \
                                        -Dsonar.organization=dmtorrico-1 \
                                        -Dsonar.projectKey=dmtorrico_spring-boot-h2-database-crud \
                                        -Dsonar.projectName=spring-boot-h2-database-crud \
                                        -Dsonar.token=${SONAR_TOKEN} \
                                        -X
                                    """
                            }
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