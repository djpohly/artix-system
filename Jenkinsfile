node {
    stage('Checkout') {
        checkout scm
        sh "create_build_queue.sh"
    }
    stage('Build') {
        sh "build_queue.sh"
    }
    stage('Repo') {
        sh "repo.sh"
    }
}
