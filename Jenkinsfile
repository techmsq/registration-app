pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        MAVEN_HOME = tool 'Maven3'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/techmsq/register-app'
            }
        }

        stage('Build Application') {
            steps {
                script {
                    sh "${MAVEN_HOME}/bin/mvn clean package -B"
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    sh "${MAVEN_HOME}/bin/mvn test -B"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and tests completed successfully.'
        }
        failure {
            echo 'Build or tests failed. Please check the logs for details.'
        }
    }
}
