pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Branch: ${env.BRANCH_NAME}"
                echo "Build #: ${env.BUILD_NUMBER}"
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Simulating build...'
                sh 'mkdir -p build && echo "ok" > build/result.txt'
            }
        }

        stage('Security Placeholder') {
            steps {
                echo 'Security scans will go here...'
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Only runs on main branch!'
            }
        }
    }

    post {
        always {
            echo "Pipeline finished with status: ${currentBuild.result}"
        }
    }
}
