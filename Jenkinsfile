pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
    }

    environment {
        MAVEN_VERSION = '3.9.5'
        MAVEN_HOME = "${WORKSPACE}/maven"
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
        GIT_URL = 'https://github.com/techmsq/register-app'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Install Maven') {
            steps {
                script {
                    echo "Installing Maven ${MAVEN_VERSION}..."
                    sh '''
                        MAVEN_DOWNLOAD_URL="https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz"
                        curl -sSL ${MAVEN_DOWNLOAD_URL} -o maven.tar.gz
                        mkdir -p ${MAVEN_HOME}
                        tar -xzf maven.tar.gz -C ${MAVEN_HOME} --strip-components=1
                        rm maven.tar.gz
                        chmod +x ${MAVEN_HOME}/bin/mvn
                    '''
                    sh "${MAVEN_HOME}/bin/mvn -v"
                }
            }
        }

        stage('Debug Environment') {
            steps {
                sh 'env'
                sh 'mvn -v'
                sh 'java -version'
            }
        }

        stage('Checkout from SCM') {
            steps {
                script {
                    try {
                        git branch: "${GIT_BRANCH}", credentialsId: 'github', url: "${GIT_URL}"
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
                        sh 'mvn clean package -B'
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
                        sh 'mvn test -B'
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
