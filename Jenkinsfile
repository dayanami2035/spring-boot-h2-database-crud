pipeline {
    agent any

    tools {
        maven 'maven-default'
    }

    environment {
        SONAR_SERVER = 'sonarqube-server'
        // EXTRAEMOS DINÁMICAMENTE EL NOMBRE DEL REPO
        // Esto evita que tengas que cambiar el nombre manualmente en cada proyecto
        REPO_NAME = "${env.GIT_URL.split('/').last().split('\\.').first()}"
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

        stage('Parallel Build And Sonar') {

            parallel {

                stage('Build') {
                    steps {
                        sh 'mvn package -DskipTests'
                    }
                }

                stage('SonarQube Analysis') {
                    steps {

                        withSonarQubeEnv('SonarQube') {

                            sh """
                                mvn sonar:sonar \
                                -Dsonar.organization=dmtorrico \
                                -Dsonar.projectKey=${env.REPO_NAME} \
                                -Dsonar.projectName=${env.REPO_NAME}
                            """
                        }
                    }
                }
            }
        }

        stage('Archive JAR') {
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

        always {
            echo 'Fin de ejecución'
        }
    }
}