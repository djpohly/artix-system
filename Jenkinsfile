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
                DEPLOY = ''
            }
            steps {
                sh '''
                    PACKAGE = 'none'
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            ${CMD} -p ${PACKAGE} -u -z ${REPO_NAME}
                        fi
                    done
                    echo ${PACKAGE} > package.txt
                '''
            }
            post {
                success {
                    script {
                        DEPLOY = readFile('package.txt')
                    }
                    sh '''
                        echo ${DEPLOY}
                        echo ${REPO_NAME}
                        if [[ ${DEPLOY} != 'none' ]]; then
                            deploypkg -p ${DEPLOY} -r ${REPO_NAME} -x
                        fi
                    '''
                }
            }
        }
    }
}
