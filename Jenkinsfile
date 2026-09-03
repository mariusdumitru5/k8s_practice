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
                        
                        kubectl create namespace robot-app || true
                          
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
                        sh """
                            echo "Creazione del database mariadb..."

                           # Inietta le variabili nel Secret ed esegue l'apply da standard input
                            python3 -c 'import os, sys, string; print(string.Template(sys.stdin.read()).safe_substitute(os.environ))' < manifests/01-mariadb-secret.yaml | kubectl apply -n robot-app -f -
                            
                            
                            kubectl apply -f manifests/02-mariadb-pvc.yaml  -n robot-app
                            kubectl apply -f manifests/03-mariadb-deployment.yaml  -n robot-app

                            echo "Creazione database completata con successo!"
                        """
                }
            }
        }
        stage("Creazione pod e deployment nginx") {
            steps {
                sh """
                        echo "Creazione pod e deployment..."
                        
                        kubectl apply  -f manifests/04-nginx-configmap.yaml -n robot-app
                        kubectl apply  -f manifests/05-nginx-deployment.yaml -n robot-app
                        
                        echo "Creazione pod e deployment completata con successo!"
                """
            }
        }
        stage("Creazione backend") {
            steps {
                sh """
                        echo "Creazione backend..."
                        
                        kubectl apply  -f manifests/backend-configmap.yaml -n robot-app
                        kubectl apply  -f manifests/backend-deployment.yaml -n robot-app
                        
                        echo "Creazione backend completata con successo!"
                """
            }
        }
    }
}