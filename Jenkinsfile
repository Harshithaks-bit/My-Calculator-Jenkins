
pipeline {
    agent any

    tools {
        jdk 'JDK 25'
        maven 'Maven-3.10'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Pulling latest code from GitHub...'
                checkout scm
            }
        }

        stage('Static Verification') {
            steps {
                echo 'Validating Maven project...'
                bat 'mvn validate'
            }
        }

        stage('Build and Test') {
            steps {
                echo 'Compiling code and running unit tests...'
                bat 'mvn clean verify'
            }

            post {
                always {
                    echo 'Publishing JUnit test results...'
                    junit allowEmptyResults: true,
                          testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Starting SonarQube code analysis...'

                withSonarQubeEnv('SonarQube') {
                    bat '''
                        mvn sonar:sonar ^
                        -Dsonar.projectKey=my-first-project ^
                        -Dsonar.projectName=MyFirstProject
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Build and SonarQube analysis completed!'
        }

        failure {
            echo 'FAILURE: Check Jenkins console output.'
        }
    }
}
