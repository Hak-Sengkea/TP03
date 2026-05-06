pipeline{
    agent {
        // dockerfile {
        //     filename 'agent/dockerfile'
        //     dir 'agent'
        // }
        label 'laravel-agent'
    }
    stages{
        stage('Build') {
            steps {
                echo 'Building ...'
                checkout scm

                //configure database
                // sh 'sed -i "s/DB_CONNECTION=.*/DB_CONNECTION=sqlite/" .env'
                // sh 'sed -i "s/DB_DATABASE=.*/DB_DATABASE=\/var\/www\/html\/database\/database.sqlite/" .env'
                // sh 'sed -i "s/DB_USERNAME=.*/DB_USERNAME=root/" .env'
                // sh 'sed -i "s/DB_PASSWORD=.*/DB_PASSWORD=/" .env'
                // sh 'sed -i "s/DB_HOST=.*/DB_HOST=127.0.0.1/" .env'

                echo 'copy env file ...'
                sh 'cp .env.example .env'

                echo 'Installing dependencies ...'
                sh 'composer install'
                sh 'npm install'

                echo 'Generating app key ...'
                sh 'php artisan key:generate'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing ...'
                sh 'php artisan test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying ...'
                sh 'ansible-playbook -i inventory/hosts.ini playbook.yml'
            }
        }
    }
    post {
    failure {
        sh '''
        curl -s -X POST "https://api.telegram.org/botYOUR_BOT_TOKEN/sendMessage" \
          -d chat_id="YOUR_CHAT_ID" \
          -d text="❌ Jenkins build failed

Job: ${JOB_NAME}
Build: #${BUILD_NUMBER}
URL: ${BUILD_URL}"
        '''
    }

    success {
        sh '''
        curl -s -X POST "https://api.telegram.org/botYOUR_BOT_TOKEN/sendMessage" \
          -d chat_id="YOUR_CHAT_ID" \
          -d text="✅ Jenkins build successful

Job: ${JOB_NAME}
Build: #${BUILD_NUMBER}
URL: ${BUILD_URL}"
        '''
    }
}
}