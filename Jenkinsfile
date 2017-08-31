pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    GIT_COMMIT_FILES=$(git show --pretty=format: --name-only ${GIT_COMMIT})
                    echo "GIT_COMMIT_FILES: ${GIT_COMMIT_FILES[@]}"
                    REPO='system'
                    case ${BRANCH_NAME} in
                        'testing'|'staging')
                            REPO=${REPO}-${BRANCH_NAME}
                            BUILDPKG="buildpkg-${BRANCH_NAME}"
                        ;;
                        'master')
                            BUILDPKG="buildpkg"
                        ;;
                        PR-*)
                            REPO=${REPO}-testing
                            BUILDPKG="buildpkg-testing"
                        ;;
                    esac
                    echo ${BUILDPKG} > cmd.txt
                    echo ${REPO} > repo.txt
                    PACKAGE=none
                    for f in ${GIT_COMMIT_FILES[@]};do
                        echo "tracked changed file: $f"
                        if [[ "$f" == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            echo ${PACKAGE}
                        fi
                    done
                    echo ${PACKAGE} > package.txt
                '''
            }
        }
        stage('Build') {
            environment {
                BUILDPKG = readFile('cmd.txt')
                REPO = readFile('repo.txt')
                PACKAGE = readFile('package.txt')
            }
            steps {
                sh '''
                    echo "REPO: ${REPO}"
                    echo "PACKAGE: ${PACKAGE}"
                    echo "BUILDPKG: ${BUILDPKG}"
                    if [[ "${PACKAGE}" != none ]]; then
                        ${BUILDPKG} -p ${PACKAGE} -u -z ${REPO}
                    fi
                '''
            }
            post {
                success {
                    sh '''
                        echo "REPO: ${REPO}"
                        echo "PACKAGE: ${PACKAGE}"
                        if [[ "${PACKAGE}" != none ]]; then
                            deploypkg -p ${PACKAGE} -r ${REPO} -x
                        fi
                    '''
                }
            }
        }
    }
}
