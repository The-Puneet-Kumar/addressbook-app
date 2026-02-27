pipeline {
    agent any

    environment {
        TOMCAT_PATH = '/home/ubuntu/apache-tomcat-9.0.115/webapps'
        TOMCAT_BIN  = '/home/ubuntu/apache-tomcat-9.0.115/bin'
        APP_NAME = 'addressbook-app'
        MAVEN_OPTS = '-Xmx512m'
    }

    tools {
        maven 'Maven'
    }

    options {
        skipDefaultCheckout(true)
        durabilityHint('PERFORMANCE_OPTIMIZED')
        timestamps()
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/The-Puneet-Kumar/addressbook-app.git'
            }
        }

        stage('Build WAR Fast') {
            steps {
                sh '''
                echo "Building WAR..."
                mvn -B -T 1C clean package -DskipTests
                '''
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh '''
                echo "Stopping Tomcat..."
                pkill -f tomcat || true
                sleep 5

                echo "Removing old deployment..."
                rm -rf $TOMCAT_PATH/$APP_NAME
                rm -f $TOMCAT_PATH/$APP_NAME.war

                echo "Copying new WAR..."
                cp target/$APP_NAME.war $TOMCAT_PATH/

                echo "Starting Tomcat..."
                cd $TOMCAT_BIN
                ./startup.sh

                echo "Deployment Completed"
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build & Deployment Successful 🚀"
        }
        failure {
            echo "❌ Something went wrong. Check console logs."
        }
    }
}
