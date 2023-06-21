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
        stage('Deploy'){
            docker.image('cdrx/pyinstaller-linux:python2').inside{
                checkout scm
                sh 'pyinstaller --onefile sources/add2vals.py'
                sleep 60
                archiveArtifacts 'dist/add2vals'
            }
        }
    }catch(e){
        throw e
    }
}