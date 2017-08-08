node {
    stage 'Checkout' {
        checkout scm
    }
    stage 'Build' {
        sh "for pkg in *; do buildpkg -u -x -z system -p $pkg || true; done"
    }
}
