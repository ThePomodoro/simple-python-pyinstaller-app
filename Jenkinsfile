pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') { 
            agent {
                docker {
                    image 'qnib/pytest' 
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py' 
            }
            post {
                always {
                    junit 'test-reports/results.xml' 
                }
            }
        }
        stage('Lint') { 
            agent {
	        docker {
                    image 'eeacms/pylint'
 		}
	    }
            environment { 
                VOLUME = '$(pwd)/sources:/test_calc.py'
                IMAGE = 'eeacms/pylint'
            }
            steps { 
                sh "docker run --rm -v ${VOLUME} ${IMAGE}" 
            }
        }
    }
}