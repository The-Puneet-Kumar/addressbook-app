pipeline {
    agent any

    tools {
        maven 'Maven'     // Name of Maven in Jenkins configuration
        jdk 'Java21'      // Name of JDK in Jenkins configuration
    }

    environment {
        TOMCAT_DIR = '/home/ubuntu/apache-tomcat-9.0.115'
        APP_NAME   = 'addressbook-app'
    }

    stages {

        stage('Build WAR') {
            steps {
                echo "Building WAR file..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying WAR to Tomcat..."
                sh '''
                TOMCAT_DIR=/home/ubuntu/apache-tomcat-9.0.115
                APP_NAME=addressbook-app

                # Ensure Tomcat folders have correct permissions
                sudo chown -R jenkins:jenkins $TOMCAT_DIR
                sudo chmod -R 755 $TOMCAT_DIR/bin/*.sh
                sudo chmod -R 755 $TOMCAT_DIR/logs $TOMCAT_DIR/temp $TOMCAT_DIR/work $TOMCAT_DIR/webapps

                # Stop Tomcat gracefully if running
                $TOMCAT_DIR/bin/shutdown.sh || true

                # Wait max 120 sec for shutdown
                timeout=120
                elapsed=0
                while pgrep -f 'tomcat' > /dev/null; do
                    if [ $elapsed -ge $timeout ]; then
                        echo "Tomcat did not stop within $timeout sec!"
                        exit 1
                    fi
                    echo "Waiting for Tomcat to stop... ($elapsed sec)"
                    sleep 5
                    elapsed=$((elapsed+5))
                done

                # Remove old deployment
                rm -rf $TOMCAT_DIR/webapps/$APP_NAME $TOMCAT_DIR/webapps/$APP_NAME.war

                # Copy new WAR
                cp target/$APP_NAME.war $TOMCAT_DIR/webapps/

                # Start Tomcat
                $TOMCAT_DIR/bin/startup.sh
                sleep 15

                # Verify Tomcat started
                if ! pgrep -f 'tomcat' > /dev/null; then
                    echo "Tomcat failed to start!"
                    exit 1
                fi

                echo "Deployment successful!"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed: Deployment Successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
