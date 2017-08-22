node {
    stage('Checkout') {
        checkout scm
        sh "create_build_queue.sh"
    }
    stage('Build') {
        sh "/home/jenkins/workspace/build_queue.sh"
    }
    stage('Repo') {
        sh "/home/jenkins/workspace/repo.sh"
    }
}
