pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo 'GitHub + Local changes'
            }
        }

        stage('Trivy: file system scan') {
            steps {
                sh 'trivy fs . --config /dev/null'
            }
        }

        stage('OWASP: Dependency Check') {
            steps {
                dependencyCheck(
                    additionalArguments: '--scan . --disableYarnAudit --disableNodeAudit',
                    odcInstallation: 'OWASP',
                    nvdCredentialsId: 'nvd-api-key'
                )
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    script {
                        def scannerHome = tool 'Sonar'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=wanderlust \
                            -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

                        docker build -t $DOCKER_USERNAME/wanderlust-backend:${BUILD_NUMBER} ./backend
                        docker build -t $DOCKER_USERNAME/wanderlust-frontend:${BUILD_NUMBER} ./frontend

                        docker push $DOCKER_USERNAME/wanderlust-backend:${BUILD_NUMBER}
                        docker push $DOCKER_USERNAME/wanderlust-frontend:${BUILD_NUMBER} ̰

                        docker logout
            '''
        } 
    }
}
    }
}