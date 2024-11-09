pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Cloner le dépôt
                git 'https://github.com/FadwaLacham/DevSecOps.git'
            }
        }
         stage('Secret Scanning') {
            steps {
                bat 'gitleaks detect --source . --report-format json --report-path gitleaks-report.json'
            }
        }

        stage('Analyze Secrets Report') {
            steps {
                script {
                    def report = readFile('gitleaks-report.json')
                    if (report.contains('leak')) {
                        error 'Secrets détectés ! Le build est arrêté.'
                    }
                }
            }
        }

stage('SCA with Dependency-Check') {
    steps {
        echo 'Analyse de la composition des sources avec OWASP Dependency-Check...'
        bat '"C:\\dependency-check\\bin\\dependency-check.bat" --project "demo" --scan . --format HTML --out dependency-check-report.html --nvdApiKey 181c8fc5-2ddc-4d15-99bf-764fff8d50dc --disableAsse'
    }
}


stage('ZAP Security Scan') {
    steps {
        script {
            def appUrl = 'https://ed3a-105-73-96-62.ngrok-free.app'

            // Start spider scan
            bat "curl \"http://localhost:8095/JSON/spider/action/scan/?url=${appUrl}&recurse=true\""

            // Poll for spider scan status
            def spiderStatus
            while (true) {
                spiderStatus = bat(script: 'curl -s "http://localhost:8095/JSON/spider/view/status/"', returnStdout: true).trim()
                if (spiderStatus == '100') {
                    break
                }
                echo "Spider scan progress: ${spiderStatus}%"
                bat 'timeout /t 5' // Wait for 5 seconds before checking again
            }

            // Start active scan
            bat "curl \"http://localhost:8095/JSON/ascan/action/scan/?url=${appUrl}&recurse=true\""

            // Poll for active scan status
            def ascanStatus
            while (true) {
                ascanStatus = bat(script: 'curl -s "http://localhost:8095/JSON/ascan/view/status/"', returnStdout: true).trim()
                if (ascanStatus == '100') {
                    break
                }
                echo "Active scan progress: ${ascanStatus}%"
                bat 'timeout /t 5' // Wait for 5 seconds before checking again
            }
        }
    }
    post {
        always {
            // Generate report
            bat 'curl "http://localhost:8095/OTHER/core/other/htmlreport/" > zap_report.html'
        }
    }
}


        stage('Build') {
            steps {
                // Construire le projet Maven
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                // Exécuter les tests
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                // Déploiement (peut être remplacé par votre stratégie de déploiement)
                sh 'echo "Déploiement de l\'application..."'
            }
        }
    }

    post {
        always {
            // Archive les fichiers importants à la fin
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
        success {
            // Notifier en cas de succès
            echo 'Le build a réussi !'
        }
        failure {
            // Notifier en cas d'échec
            echo 'Le build a échoué.'
        }
    }
}
