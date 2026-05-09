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

                                sh '''
                                    mvn sonar:sonar \
                                    -Dsonar.organization=dmtorrico \
                                    -Dsonar.projectKey=dmtorrico_spring-boot-h2-database-crud \
                                    -Dsonar.projectName=dmtorrico_spring-boot-h2-database-crud
                                '''
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