pipeline {
    agent {label "mac-agent"}
    stages {
        stage("Checkout"){
            steps {
                 checkout scm
            }
        }
        stage("Create Namespace") {
            steps {
                sh """
                        echo "Creazione namespace per il deploy..."
                        
                        kubectl create namespace robot-app
                          
                        echo "Creazione namespace completata con successo!"
                """
            }
        }
    }
}