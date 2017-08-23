pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''
                    GIT_COMMIT=$(git rev-parse HEAD)
                    DEST=$(git show --pretty=format: --name-only ${GIT_COMMIT})
                    TMP=${JOB_NAME%/*}
                    POOL_NAME=${TMP#*/}
                    REPO_NAME=${POOL_NAME}
                    [[ ${BRANCH_NAME} == 'testing' ]] && REPO_NAME=${REPO_NAME}-${BRANCH_NAME}
                    for f in ${DEST[@]};do
                        if [[ $f == */PKGBUILD ]];then
                            PACKAGE=${f%/PKGBUILD}
                            source $f
                            ARCH=${arch}
                            VERSION=${pkgver}
                            RELEASE=${pkgrel}
                            DEPLOY=(${pkgname[@]})
                            echo  "buildpkg -p ${PACKAGE} -cuslx -z ${REPO_NAME}"
                        fi
                    done
                    '''
      }
      post {
        success {
          steps() {
            sh '''
                            for pkg in ${DEPLOY[@]};do
                                echo "deploypkg -x -p ${pkg}-${VERSION}-${RELEASE}-${ARCH}.${EXT} -r ${REPO_NAME}"
                            done
                            '''
            echo 'Successfuuly deployed'
          }


        }

      }
    }
  }
  environment {
    GIT_COMMIT = ''
    DEST = ''
    PACKAGE = ''
    REPO_NAME = ''
    ARCH = ''
    VERSION = ''
    RELEASE = ''
    DEPLOY = ''
    EXT = 'pkg.tar.xz'
  }
}
