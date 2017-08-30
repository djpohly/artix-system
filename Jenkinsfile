pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    GIT_COMMIT_FILES=$(git show --pretty=format: --name-only ${GIT_COMMIT})
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
                    PACKAGE='none'
                    for f in ${GIT_COMMIT_FILES[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                        fi
                    done
                    echo ${PACKAGE} > package.txt
                '''
            }
        }
        stage('Build') {
            environment {
                CMD = readFile('cmd.txt')
                REPO_NAME = readFile('repo.txt')
                PACKAGE = readFile('package.txt')
            }
            steps {
                sh '''
                    echo "REPO_NAME: ${REPO_NAME}"
                    echo "PACKAGE: ${PACKAGE}"
                    if [[ "${PACKAGE}" != 'none' ]]; then
                        ${CMD} -p ${PACKAGE} -u -z ${REPO_NAME}
                    fi
                '''
            }
            post {
                success {
                    sh '''
                        echo "REPO_NAME: ${REPO_NAME}"
                        echo "PACKAGE: ${PACKAGE}"
                        if [[ "${PACKAGE}" != 'none' ]]; then
                            deploypkg -p ${PACKAGE} -r ${REPO_NAME} -x
                        fi
                    '''
                }
            }
        }
    }
}
