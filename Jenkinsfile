node{
    stage('Build'){
        docker.image('python:2-alpine').inside{
            checkout scm
            sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'

        }
    }
    try{
        stage('Test'){
            docker.image('qnib/pytest').inside{
                checkout scm
                sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py'
            }
        }
    } finally {
        junit 'test-reports/results.xml'
    }

    stage('Manual Approval'){
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    try{
        withEnv(['VOLUME=$(pwd)/sources:/src','IMAGE=cdrx/pyinstaller-linux:python2']){
                stage('Deploy'){
                dir(path: env.BUILD_ID) { 
                        checkout scm
                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 

                    }
                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"

                sleep 60
            }
        }
    }catch(e){
        throw e
    }
}