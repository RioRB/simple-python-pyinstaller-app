node {

    withDockerContainer('python:2-alpine') {
        stage('Build') {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    withDockerContainer('qnib/pytest') {
        stage('Test') {
            checkout scm
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }

    withDockerContainer('cdrx/pyinstaller-linux:python2') {
        try {
            stage('Deliver') {
                checkout scm
                ssh 'pyinstaller --onefile sources/add2vals.py'
            }
            archiveArtifacts 'dist/add2vals'
        } catch(e) {
            throw e
        }
    }

}