pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    DEST=$(git show --pretty=format: --name-only ${GIT_COMMIT})
                    REPO_NAME='system'
                    PACKAGE='none'
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
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            ${CMD} -p ${PACKAGE} -u -z ${REPO_NAME}
                        fi
                    done
                    echo "${REPO_NAME}" > repo.txt
                    echo "${PACKAGE}" > package.txt
                '''
            }
            post {
                success {
                    script {
                        repo = readFile("repo.txt")
                        package = readFile("package.txt")
                    }
                    echo ${repo}
                    echo ${package}
                    sh '''
                        echo "deploypkg -p ${package} -r ${repo} -x"
                    '''
                }
            }
        }
    }
}
