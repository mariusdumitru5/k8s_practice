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
        stage("Creazione database mariadb") {
            steps {
                withCredentials([
                    string(credentialsId: 'mariadb-root-pass', variable: 'MYSQL_ROOT_PASSWORD'),
                    string(credentialsId: 'mariadb-user-pass', variable: 'MYSQL_PASSWORD')
                ]) {
                    sh 'envsubst < secret-template.yaml | kubectl apply -f -'
                }

                sh """
                        echo "Creazione del database mariadb..."
                        
                       # Inietta le variabili nel Secret ed esegue l'apply da standard input
                        envsubst < 01-mariadb-secret.yaml | kubectl apply -n robot-app -f -
                        kubectl apply -f 02-mariadb-pvc.yaml  -n robot-app
                        kubectl apply -f 03-mariadb-deployment.yaml  -n robot-app
                        
                        echo "Creazione database completata con successo!"
                """
            }
        }
        stage("Creazione pod e deployment nginx") {
            steps {
                sh """
                        echo "Creazione pod e deployment..."
                        
                        kubectl apply  04-nginx-configmap.yaml -n robot-app
                        kubectl apply  05-nginx-deployment.yaml -n robot-app
                        
                        echo "Creazione pod e deployment completata con successo!"
                """
            }
        }
        stage("Creazione backend") {
            steps {
                sh """
                        echo "Creazione backend..."
                        
                        kubectl apply  backend-configmap.yaml -n robot-app
                        kubectl apply  backend-deployment.yaml -n robot-app
                        
                        echo "Creazione backend completata con successo!"
                """
            }
        }
    }
}