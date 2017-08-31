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
                            BUILDPKG="buildpkg-${BRANCH_NAME}"
                        ;;
                        'master')
                            BUILDPKG="buildpkg"
                        ;;
                        PR-*)
                            REPO_NAME=${REPO_NAME}-testing
                            BUILDPKG="buildpkg-testing"
                        ;;
                    esac
                    echo ${BUILDPKG} > cmd.txt
                    echo ${REPO_NAME} > repo.txt
                    PACKAGE='none'
                    for f in ${GIT_COMMIT_FILES[@]};do
                        [[ $f == */PKGBUILD ]] && PACKAGE=${f%/PKGBUILD}
                    done
                    echo ${PACKAGE} > package.txt
                '''
            }
        }
        stage('Build') {
            environment {
                BUILDPKG = readFile('cmd.txt')
                REPO_NAME = readFile('repo.txt')
                PACKAGE = readFile('package.txt')
            }
            steps {
                sh '''
                    echo "REPO_NAME: ${REPO_NAME}"
                    echo "PACKAGE: ${PACKAGE}"
                    if [[ "${PACKAGE}" != 'none' ]]; then
                        ${BUILDPKG} -p ${PACKAGE} -u -z ${REPO_NAME}
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
