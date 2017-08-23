pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    DEST=$(git show --pretty=format: --name-only "${GIT_COMMIT}")
                    POOL_NAME=${JOB_NAME%/*}
                    REPO_NAME=${POOL_NAME#*/}
                    [[ ${BRANCH_NAME} == "testing" ]] && REPO_NAME=${REPO_NAME}-${BRANCH_NAME}
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            echo  "buildpkg -p ${PACKAGE} -cuslx -z ${REPO_NAME}"
                        fi
                    done
                    '''
            }
            post {
                success {
                    echo 'success'
                }
            }
        }
    }
    environment {
        GIT_COMMIT = ''
        DEST = ''
        PACKAGE = ''
        REPO_NAME = ''
    }
}
