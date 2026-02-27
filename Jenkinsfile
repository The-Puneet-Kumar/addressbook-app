pipeline {
    agent any

    environment {
        MAVEN_OPTS = '-Xmx1024m'
        TOMCAT_PATH = '/home/ubuntu/apache-tomcat-9.0.115/webapps'
        APP_NAME = 'addressbook-app'
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

        stage('Clean Old Deployment') {
            steps {
                sh '''
                rm -rf $TOMCAT_PATH/$APP_NAME
                rm -f $TOMCAT_PATH/$APP_NAME.war
                '''
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh '''
                cp target/$APP_NAME.war $TOMCAT_PATH/
                '''
            }
        }

        stage('Restart Tomcat') {
            steps {
                sh '''
                cd /home/ubuntu/apache-tomcat-9.0.115/bin
                ./shutdown.sh
                ./startup.sh
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful 🚀"
        }
        failure {
            echo "❌ Deployment Failed"
        }
    }
}
