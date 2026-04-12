pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Branch: ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Simulating build...'
                sh 'mkdir -p build && echo "ok" > build/result.txt'
            }
        }

        // ── FIRST REAL SECURITY STAGE ────────────────────────────

        stage('Secrets Detection — Gitleaks') {
            steps {
                script {
                    def exitCode = sh(
                        script: '''
                            gitleaks detect \
                                --source . \
                                --report-format json \
                                --report-path gitleaks-report.json \
                                --redact \
                                --no-git \
                                --verbose
                        ''',
                        returnStatus: true  // don't fail immediately, we handle it below
                    )

                    // Archive the report regardless of result
                    archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true

                    // Now enforce the gate
                    if (exitCode == 1) {
                        error("🚨 Gitleaks found secrets in the code! Check gitleaks-report.json")
                    } else if (exitCode == 126 || exitCode == 127) {
                        error("❌ Gitleaks not found on agent. Please install it first.")
                    } else {
                        echo "✅ No secrets detected."
                    }
                }
            }
        }

        // ─────────────────────────────────────────────────────────

        stage('Security Placeholder') {
            steps {
                echo 'More security stages coming here...'
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
            echo "Pipeline finished with status: ${currentBuild.currentResult}"
        }
        failure {
            echo "Pipeline failed — review the archived gitleaks-report.json"
        }
    }
}
