pipeline {
    agent any

    environment {
        MAVEN_OPTS = '-Xmx1024m'
        TOMCAT_PATH = '/home/ubuntu/apache-tomcat-9.0.115/webapps'
        APP_NAME = 'addressbook-app'
    }

    tools {
        maven 'Maven'   // Make sure Maven is configured in Jenkins
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/The-Puneet-Kumar/addressbook-app.git',
                    branch: 'main'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Verify WAR File') {
            steps {
                sh 'ls -l target'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh '''
                rm -rf $TOMCAT_PATH/$APP_NAME
                rm -f $TOMCAT_PATH/$APP_NAME.war
                cp target/$APP_NAME.war $TOMCAT_PATH/
                '''
            }
        }

        stage('Restart Tomcat') {
            steps {
                sh '''
                cd /home/ubuntu/apache-tomcat-9.0.115/bin
                ./shutdown.sh || true
                sleep 5
                ./startup.sh
                '''
            }
        }
    }

    post {
        success {
            echo "✅ WAR Built & Deployed Successfully 🚀"
        }
        failure {
            echo "❌ Build or Deployment Failed"
        }
    }
}
