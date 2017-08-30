pipeline {
    agent any
    environment {
        PKG = ''
        REPO = ''
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    DEST=$(git show --pretty=format: --name-only ${GIT_COMMIT})
                    REPO_NAME='system'
                    case ${BRANCH_NAME} in
                        'testing'|'staging')
                            REPO_NAME=${REPO_NAME}-${BRANCH_NAME}
                            CMD="buildpkg-${BRANCH_NAME}"
                        ;;
                        'master')
                            CMD="buildpkg"
                        ;;
                        PR-*)
                            REPO_NAME=${REPO_NAME}-testing
                            CMD="buildpkg-testing"
                        ;;
                    esac
                    echo ${REPO_NAME} > repo.txt
                    PACKAGE='none'
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            ${CMD} -p ${PACKAGE} -u -z ${REPO_NAME}
                        fi
                    done
                    echo ${PACKAGE} > package.txt
                '''
            }
        }
        stage('Deloyment') {
            steps {
                script {
                    PKG = readFile('package.txt')
                    REPO = readFile('repo.txt')
                }
                sh '''
                    if [[ ${PKG} != 'none' ]]; then
                        echo "deploypkg -p ${PKG} -r ${REPO} -x"
                    fi
                '''
            }
        }
    }
}
