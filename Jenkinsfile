pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'Java21'
    }

    environment {
        TOMCAT_DIR = '/home/ubuntu/apache-tomcat-9.0.115'
        APP_NAME = 'addressbook-app'
    }

    stages {

        stage('Build WAR') {
            steps {
                echo "Building WAR..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Tomcat (Fast)') {
            steps {
                echo "Deploying WAR to running Tomcat..."
                sh '''
                TOMCAT_DIR=/home/ubuntu/apache-tomcat-9.0.115
                APP_NAME=addressbook-app

                # Remove old WAR only, keep exploded folder for hot deploy
                rm -f ${TOMCAT_DIR}/webapps/${APP_NAME}.war

                # Copy new WAR
                cp target/${APP_NAME}.war ${TOMCAT_DIR}/webapps/

                echo "WAR copied. Tomcat will auto-deploy the new version."
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed!'
        }
    }
}
