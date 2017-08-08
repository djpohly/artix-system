node {
    stage 'Checkout'
    checkout scm
    stage 'Build'
    dir('/var/lib/jenkins/.jenkins/workspace/artix-linux/system') {
        sh "for pkg in *; do buildpkg -u -x -z system -p $pkg || true; done"
    }
}
