pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        TOMCAT_DIR = '/home/ubuntu/apache-tomcat-9.0.115'
        APP_NAME = 'addressbook-app'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/The-Puneet-Kumar/addressbook-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Stopping Tomcat..."
                pkill -f tomcat || true
                sleep 5

                echo "Removing old app..."
                rm -rf $TOMCAT_DIR/webapps/$APP_NAME*
                
                echo "Copying new WAR..."
                cp target/$APP_NAME.war $TOMCAT_DIR/webapps/

                echo "Starting Tomcat..."
                $TOMCAT_DIR/bin/startup.sh
                '''
            }
        }
    }
}
