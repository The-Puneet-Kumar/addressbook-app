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
                sh 'mvn clean package -DskipTests'
            }
        }

      stage('Deploy to Tomcat') {
    steps {
        sh '''
        TOMCAT_DIR=/home/ubuntu/apache-tomcat-9.0.115
        APP_NAME=addressbook-app

        echo "Stopping Tomcat gracefully..."
        ${TOMCAT_DIR}/bin/shutdown.sh || true
        # Wait until Tomcat stops
        while pgrep -f 'tomcat' > /dev/null; do
            echo "Waiting for Tomcat to stop..."
            sleep 2
        done

        echo "Removing old app and WAR"
        rm -rf ${TOMCAT_DIR}/webapps/${APP_NAME} ${TOMCAT_DIR}/webapps/${APP_NAME}.war

        echo "Copying new WAR"
        cp target/${APP_NAME}.war ${TOMCAT_DIR}/webapps/

        echo "Starting Tomcat..."
        ${TOMCAT_DIR}/bin/startup.sh
        sleep 5

        # Check if Tomcat started
        if ! pgrep -f 'tomcat' > /dev/null; then
            echo "Tomcat failed to start!"
            exit 1
        else
            echo "Tomcat started successfully."
        fi
        '''
    }
}
    } // end of stages

    post {
        success {
            echo 'Deployment Successful'
        }
        failure {
            echo 'Build Failed'
        }
    }

} // end of pipeline
