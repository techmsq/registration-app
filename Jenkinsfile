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

        stage('Debug Environment') {
            steps {
                echo 'Checking environment configuration...'
                sh 'env'
                sh 'mvn -v'
                sh 'java -version'
            }
        }

        stage('Checkout from SCM') {
            steps {
                script {
                    try {
                        git branch: 'main', credentialsId: 'github', url: 'https://github.com/techmsq/registration-app'
                    } catch (Exception e) {
                        echo "SCM checkout failed: ${e}"
                        error("Checkout failed. Please verify Git credentials and repository URL.")
                    }
                }
            }
        }

        stage('Build Application') {
            steps {
                script {
                    try {
                        sh "${MAVEN_HOME}/bin/mvn clean package -B"
                    } catch (Exception e) {
                        echo "Build failed: ${e}"
                        error("Build stage failed. Check Maven configuration and logs for details.")
                    }
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    try {
                        sh "${MAVEN_HOME}/bin/mvn test -B"
                    } catch (Exception e) {
                        echo "Test execution failed: ${e}"
                        error("Test stage failed. Review the test logs for errors.")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the error logs.'
        }
    }
}
