pipeline {

    agent any

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

                stage('SonarQube') {

                    steps {

                        withSonarQubeEnv('sonarqube-server') {

                            withCredentials([
                                string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')
                            ]) {

                                sh '''
                                    mvn sonar:sonar \
                                    -Dsonar.projectKey=spring-boot-h2-database-crud \
                                    -Dsonar.projectName=spring-boot-h2-database-crud \
                                    -Dsonar.token=$SONAR_TOKEN
                                '''
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