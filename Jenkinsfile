pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"

        // Define the SonarQube environment variable
        SONARQUBE_ENV = 'SonarQubeServer'

        // Define Tomcat environment variables
        TOMCAT_USER = 'admin'
        TOMCAT_PASS = '123456'
        TOMCAT_URL = 'http://3.91.230.133:8080/manager/html'
    }

    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Dadu777/java-onlinebookstore.git'
            }
        }

        stage('Maven Build') {
            steps {
                withMaven(maven: 'Maven') {
                    sh "mvn clean install"
                }
            }
        }

        stage('SonarQube Scan') {
            steps {
                // Run the SonarQube analysis
                withSonarQubeEnv(SONARQUBE_ENV) {
                    sh "mvn sonar:sonar -Dsonar.projectKey=bookstore -Dsonar.host.url=http://52.90.86.66:9000 -Dsonar.login=squ_71ffb4be4fad1d23aa5d82dc7eb3e4432f8f9595"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = 'target/onlinebookstore.war'  // Path to the WAR file
                    def tomcatManagerUrl = "${TOMCAT_URL}/manager/text/deploy?path=/onlinebookstore&update=true"
                    
                    sh """
                    curl --upload-file ${warFile} \
                    --user ${TOMCAT_USER}:${TOMCAT_PASS} \
                    ${tomcatManagerUrl}
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
