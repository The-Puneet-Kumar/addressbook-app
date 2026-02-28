pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'Java21'
    }
    environment {
        TOMCAT_DIR = '/home/ubuntu/apache-tomcat-9.0.115'
        APP_NAME   = 'addressbook-app'
    }
    stages {
        stage('Build WAR') {
            steps {
                echo "Building WAR..."
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy to Tomcat (Hot Deploy)') {
            steps {
                echo "Deploying WAR to Tomcat..."
                sh '''
                    echo "Stopping Tomcat..."
                    /home/ubuntu/apache-tomcat-9.0.115/bin/shutdown.sh || true
                    sleep 5

                    TOMCAT_PID=$(lsof -ti:9090 || true)
                    if [ ! -z "$TOMCAT_PID" ]; then
                        echo "Force killing Tomcat PID: $TOMCAT_PID"
                        kill -9 $TOMCAT_PID || true
                        sleep 3
                    else
                        echo "Tomcat already stopped."
                    fi

                    echo "Removing old WAR..."
                    rm -f  /home/ubuntu/apache-tomcat-9.0.115/webapps/addressbook-app.war || true
                    rm -rf /home/ubuntu/apache-tomcat-9.0.115/webapps/addressbook-app || true
                    rm -rf /home/ubuntu/apache-tomcat-9.0.115/work/Catalina/localhost/addressbook-app || true

                    echo "Copying new WAR..."
                    cp target/addressbook-app.war /home/ubuntu/apache-tomcat-9.0.115/webapps/

                    echo "Starting Tomcat..."
                    /home/ubuntu/apache-tomcat-9.0.115/bin/startup.sh
                    sleep 15

                    if [ -d "/home/ubuntu/apache-tomcat-9.0.115/webapps/addressbook-app" ]; then
                        echo "Deployment Successful!"
                    else
                        echo "Deployment Failed!"
                        exit 1
                    fi
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline Completed Successfully!'
        }
        failure {
            echo 'Pipeline Failed! Check logs above.'
        }
    }
}
