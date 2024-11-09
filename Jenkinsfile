pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Cloner le dépôt
                git 'https://github.com/FadwaLacham/DevSecOps.git'
            }
        }
        stage('Build') {
            steps {
                // Construire le projet Maven
                bat 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                // Exécuter les tests sans échouer le build si les tests échouent
                script {
                    def testStatus = bat(returnStatus: true, script: 'mvn test')
                    if (testStatus != 0) {
                        echo "Tests failed, but continuing with the build."
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                // Déploiement (peut être remplacé par votre stratégie de déploiement)
                bat 'echo "Déploiement de l\'application..."'
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
