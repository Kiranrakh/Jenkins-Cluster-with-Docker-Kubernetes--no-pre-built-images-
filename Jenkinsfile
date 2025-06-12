pipeline {
    agent { label 'k8s-agent' }
    stages {
        stage('Test') {
            steps {
                sh 'echo Hello from Kubernetes Agent!'
            }
        }
    }
}