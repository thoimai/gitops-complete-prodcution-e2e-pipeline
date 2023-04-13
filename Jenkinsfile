pipeline {
    agent {
        label "jenkins-agent"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
    }
    stages {
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github-pat', url: 'https://github.com/dmancloud/gitops-complete-prodcution-e2e-pipeline'
            }
        }
    }

    stages {
        stage("Update the Deployment Tags") {
            steps {
                sh """
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                """
            }
        }
    }

}