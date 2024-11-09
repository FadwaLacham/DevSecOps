pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git 'https://github.com/FadwaLacham/DevSecOps.git'
            }
        }
/*        stage('ZAP Security Scan') {
            steps {
                script {
                    def appUrl = 'https://ed3a-105-73-96-62.ngrok-free.app'
                    bat "curl \"http://localhost:8095/JSON/spider/action/scan/?url=${appUrl}&recurse=true\""
                    sleep(60)
                    bat "curl \"http://localhost:8095/JSON/ascan/action/scan/?url=${appUrl}&recurse=true\""
                    sleep(300)
                }
            }
            post {
                always {
                    bat 'curl "http://localhost:8095/OTHER/core/other/htmlreport/" > zap_report1.html'
                }
            }
        } */
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
                        error 'Secrets detected! Build is stopped.'
                    }
                }
            }
        }
        stage('SCA with Dependency-Check') {
            steps {
                echo 'Analyzing source composition with OWASP Dependency-Check...'
                bat '"C:\\dependency-check\\bin\\dependency-check.bat" --project "demo" --scan . --format HTML --out dependency-check-report.html --nvdApiKey 181c8fc5-2ddc-4d15-99bf-764fff8d50dc --disableAsse'
            }
        }
        stage('Build') {
            steps {
                // Build the Maven project
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                // Run the tests
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                // Deployment (can be replaced by your deployment strategy)
                sh 'echo "Deploying the application..."'
            }
        }
    }

    post {
        always {
            // Archive important files at the end
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
        success {
            // Notify on success
            echo 'The build succeeded!'
        }
        failure {
            // Notify on failure
            echo 'The build failed.'
        }
    }
}
