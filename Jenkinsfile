pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    DEST=$(git show --pretty=format: --name-only ${GIT_COMMIT})
                    REPO_NAME='system'
                    case ${BRANCH_NAME} in
                        'testing'|'staging') REPO_NAME=${REPO_NAME}-${BRANCH_NAME} ;;
                    esac
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            buildpkg-${BRANCH_NAME} -p ${PACKAGE} -u
                        fi
                    done
                '''
            }
            post {
                success {
                    sh '''
                        if [[ -n ${PACKAGE} ]];then
                            deploypkg -p ${PACKAGE} -r ${REPO_NAME} -x
                        fi
                    '''
                }
            }
        }
    }
    environment {
        GIT_COMMIT = ''
        DEST = ''
        PACKAGE = ''
    }
}
