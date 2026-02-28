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
                echo "Deploying WAR..."
                script {
                    // Path to WAR
                    def warFile = "target/${env.APP_NAME}.war"
                    // Check if WAR exists
                    if (!fileExists(warFile)) {
                        error "WAR file not found: ${warFile}"
                    }
                }
                // Copy WAR to Tomcat webapps
                sh '''
                TOMCAT_DIR=/home/ubuntu/apache-tomcat-9.0.115
                APP_NAME=addressbook-app

                # Ensure Tomcat folders writable (one-time setup)
                chmod -R 755 $TOMCAT_DIR/logs $TOMCAT_DIR/temp $TOMCAT_DIR/work $TOMCAT_DIR/webapps

                # Remove old WAR (optional)
                rm -f $TOMCAT_DIR/webapps/$APP_NAME.war

                # Copy new WAR
                cp target/$APP_NAME.war $TOMCAT_DIR/webapps/

                echo "WAR copied. Tomcat will auto-deploy."
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
