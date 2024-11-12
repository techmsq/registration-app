pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        GIT_URL = 'https://github.com/techmsq/registration-app'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from SCM') {
            steps {
                git branch: "${GIT_BRANCH}", credentialsId: 'github', url: "${GIT_URL}"
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace after the build.'
            cleanWs()
        }
        success {
            echo 'Build and tests completed successfully.'
        }
        failure {
            echo 'Build or tests failed.'
        }
    }
}
