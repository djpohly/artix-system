pipeline {
    agent any
    stages {
        stage('Prepare') {
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
                    echo ${CMD} > cmd.txt
                    echo ${REPO_NAME} > repo.txt
                '''
            }
        }
        stage('Build') {
            environment {
                CMD = readFile('cmd.txt')
                REPO_NAME = readFile('repo.txt')
            }
            steps {
                sh '''
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
            environment {
                PACKAGE = readFile('package.txt')
                REPO_NAME = readFile('repo.txt')
            }
            steps {
                sh '''
                    if [[ ${PACKAGE} != 'none' ]]; then
                        echo "deploypkg -p ${PKG} -r ${REPO_NAME} -x"
                    fi
                '''
            }
        }
    }
}
