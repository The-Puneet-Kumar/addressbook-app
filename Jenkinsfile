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
                    # Step 1: Force Stop Tomcat (kill if shutdown.sh fails)
                    echo "Stopping Tomcat..."
                    $TOMCAT_DIR/bin/shutdown.sh || true
                    sleep 5

                    # Force kill if still running
                    TOMCAT_PID=$(lsof -ti:9090)
                    if [ ! -z "$TOMCAT_PID" ]; then
                        echo "Force killing Tomcat on port 9090..."
                        kill -9 $TOMCAT_PID
                        sleep 3
                    fi

                    # Step 2: Clean old deployment artifacts
                    echo "Removing old WAR and extracted folder..."
                    rm -f  $TOMCAT_DIR/webapps/$APP_NAME.war
                    rm -rf $TOMCAT_DIR/webapps/$APP_NAME
                    rm -rf $TOMCAT_DIR/work/Catalina/localhost/$APP_NAME

                    # Step 3: Copy fresh WAR
                    echo "Copying new WAR..."
                    cp target/$APP_NAME.war $TOMCAT_DIR/webapps/

                    # Step 4: Start Tomcat
                    echo "Starting Tomcat..."
                    $TOMCAT_DIR/bin/startup.sh
                    sleep 15

                    # Step 5: Verify deployment
                    if [ -d "$TOMCAT_DIR/webapps/$APP_NAME" ]; then
                        echo "✅ Deployment Successful!"
                    else
                        echo "❌ Deployment Failed!"
                        exit 1
                    fi
                '''
            }
        }
    }
    post {
        success {
            echo '✅ Pipeline Completed Successfully!'
        }
        failure {
            echo '❌ Pipeline Failed! Check logs above.'
        }
    }
}
