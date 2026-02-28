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
                    # Step 1: Stop Tomcat
                    echo "Stopping Tomcat..."
                    $TOMCAT_DIR/bin/shutdown.sh || true
                    sleep 5

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

                    # Step 5: Wait and verify deployment
                    echo "Waiting for deployment..."
                    sleep 15
                    if [ -d "$TOMCAT_DIR/webapps/$APP_NAME" ]; then
                        echo "✅ Deployment Successful - App folder exists"
                    else
                        echo "❌ Deployment may have failed - App folder not found"
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
