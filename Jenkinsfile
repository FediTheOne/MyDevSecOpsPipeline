stage('Secrets Detection — Gitleaks') {
    steps {
        script {
            def exitCode = sh(
                script: '''
                    docker run --rm \
                        -v ${WORKSPACE}:/path \
                        -w /path \
                        zricethezav/gitleaks:latest detect \
                            --source . \
                            --report-format json \
                            --report-path gitleaks-report.json \
                            --redact \
                            --no-git \
                            --verbose
                ''',
                returnStatus: true
            )

            archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true

            if (exitCode == 1) {
                error("🚨 Gitleaks found secrets in the code! Check gitleaks-report.json")
            } else if (exitCode == 2) {
                error("❌ Gitleaks config/runtime error. Check pipeline logs.")
            } else {
                echo "✅ No secrets detected."
            }
        }
    }
}
