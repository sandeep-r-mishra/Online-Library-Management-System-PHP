pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/sandeep-r-mishra/Online-Library-Management-System-PHP.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${REPO_URL}", branch: 'master'
            }
        }

        stage('Install PHP & Composer') {
            steps {
                sh '''
                sudo apt-get update
                sudo apt-get install -y php php-cli php-mbstring php-xml unzip curl
                curl -sS https://getcomposer.org/installer | php
                sudo mv composer.phar /usr/local/bin/composer
                composer install || true
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

        stage('Run PHPUnit Tests') {
            when {
                expression { fileExists('vendor/bin/phpunit') }
            }
            steps {
                sh './vendor/bin/phpunit'
            }
        }
    }

    post {
        success {
            echo '✅ Build and test successful!'
        }
        failure {
            echo '❌ Build or test failed.'
        }
    }
}
