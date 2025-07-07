pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/sandeep-r-mishra/Online-Library-Management-System-PHP.git'
        DEPLOY_USER = 'root' // change if needed
        DEPLOY_HOST = '192.168.74.129'
        DEPLOY_PATH = '/var/www/html/library'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${REPO_URL}", branch: 'master'
            }
        }

        stage('Install Composer Dependencies') {
            steps {
                sh '''
                if [ -f composer.json ]; then
                  composer install || true
                fi
                '''
            }
        }

        stage('Lint PHP Files') {
            steps {
                sh '''
                find . -name "*.php" -print0 | xargs -0 -n1 php -l
                '''
            }
        }

        stage('Deploy to Apache Server') {
            steps {
                sh '''
                echo "Deploying to Apache server..."

                ssh ${DEPLOY_USER}@${DEPLOY_HOST} "rm -rf ${DEPLOY_PATH}/*"

                rsync -avz --exclude=".git" ./ ${DEPLOY_USER}@${DEPLOY_HOST}:${DEPLOY_PATH}/

                ssh ${DEPLOY_USER}@${DEPLOY_HOST} "chown -R apache:apache ${DEPLOY_PATH} && chmod -R 755 ${DEPLOY_PATH}"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Application deployed successfully!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
