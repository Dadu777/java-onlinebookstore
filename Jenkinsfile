pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/local/apache-maven-3.8.1'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"

        // Define the SonarQube environment variable
        SONARQUBE_ENV = 'SonarQubeServer'

        // Define Tomcat environment variables
        TOMCAT_USER = 'tomcat-username'
        TOMCAT_PASS = 'tomcat-password'
        TOMCAT_URL = 'http://your-tomcat-server:8080'
    }

    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/your-username/your-repo.git',
                    credentialsId: 'git-credentials-id'
            }
        }

        stage('Maven Build') {
            steps {
                withMaven(maven: 'Maven_3.8.1') {
                    sh "mvn clean install"
                }
            }
        }

        stage('SonarQube Scan') {
            steps {
                // Run the SonarQube analysis
                withSonarQubeEnv(SONARQUBE_ENV) {
                    sh "mvn sonar:sonar -Dsonar.projectKey=your-project-key -Dsonar.host.url=http://your-sonarqube-server:9000 -Dsonar.login=your-sonar-token"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = 'target/your-app.war'  // Path to the WAR file
                    def tomcatManagerUrl = "${TOMCAT_URL}/manager/text/deploy?path=/your-app&update=true"
                    
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
