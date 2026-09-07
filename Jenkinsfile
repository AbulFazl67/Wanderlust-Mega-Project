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
                dependencyCheck additionalArguments: '--scan . --disableYarnAudit --disableNodeAudit', 
                odcInstallation: 'OWASP'
            }
        }
    }
}
