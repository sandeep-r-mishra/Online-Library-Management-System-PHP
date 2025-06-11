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
                # Check if PHP is installed
                php -v || (
                    apt-get update && \
                    apt-get install -y php php-cli php-mbstring php-xml unzip curl
                )

                # Install Composer if not already present
                if ! [ -x "$(command -v composer)" ]; then
                    curl -sS https://getcomposer.org/installer | php
                    mv composer.phar /usr/local/bin/composer
                fi

                # Install dependencies if composer.json exists
                if [ -f "composer.json" ]; then
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
