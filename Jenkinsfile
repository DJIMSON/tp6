// Déclaration du pipeline Jenkins
pipeline {
    // Forcer l’exécution sur un agent Windows (assure-toi que ton nœud Windows porte bien le label "windows")
    agent { label 'windows' }

    // Variables d’environnement globales
    environment {
        DOCKER_USERNAME   = "Djimson"                              // Ton identifiant Docker Hub
        IMAGE_VERSION     = "1.${BUILD_NUMBER}"                    // Version dynamique de l’image
        DOCKER_IMAGE      = "${DOCKER_USERNAME}/tp-app:${IMAGE_VERSION}" // Nom complet de l’image Docker
        DOCKER_CONTAINER  = "ci-cd-html-css-app"                   // Nom du conteneur lors du déploiement
    }

    stages {
        // Étape 1 : Récupération du code source depuis GitHub
        stage("Checkout") {
            steps {
                git branch: 'master', url: 'https://github.com/DJIMSON/tp6.git'
            }
        }

        // Étape 2 : Exécution des tests (placeholder pour tes vrais tests)
        stage("Test") {
            steps {
                echo "Tests en cours"
            }
        }

        // Étape 3 : Création de l’image Docker
        stage("Build Docker Image") {
            steps {
                // Sur Windows, dans un bloc "bat", on utilise %VARIABLE% pour les variables d’environnement
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        // Étape 4 : Publication de l’image sur Docker Hub
        stage("Push image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'credentials_dockerHub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    bat """
                        docker login -u %DOCKER_USER% -p %DOCKER_PASSWORD%
                        echo Docker login successful
                        docker push %DOCKER_IMAGE%
                    """
                }
            }
        }

        // Étape 5 : Déploiement de l’application
        stage("Deploy") {
            steps {
                bat """
                    REM Arrête le conteneur s'il existe
                    docker container stop %DOCKER_CONTAINER% || echo "Conteneur non trouvé"
                    
                    REM Supprime le conteneur s'il existe
                    docker container rm %DOCKER_CONTAINER% || echo "Conteneur non trouvé"
                    
                    REM Lance un nouveau conteneur en arrière-plan
                    docker container run -d --name %DOCKER_CONTAINER% -p 8080:80 %DOCKER_IMAGE%
                """
            }
        }
    }
}
