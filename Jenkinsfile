pipeline {
    agent any

    options {
        buildDiscarder(logRotator(daysToKeepStr: '10', numToKeepStr: '10'))
        timeout(time: 12, unit: 'HOURS')
        timestamps()
    }

    stages {
        stage('Requirements') {
            steps {
                dir("${env.WORKSPACE}"){
                    sh 'python3 -m venv venv'
                    sh './venv/bin/pip3 install --upgrade --requirement requirements.txt'
                }
            }
        }
        stage('Lint') {
            steps {
                dir("${env.WORKSPACE}"){
                    sh 'venv/bin/flake8 --ignore=E501,E231 *.py'
                    sh 'venv/bin/pylint --errors-only --disable=C0301 --disable=C0326 *.py'
                }
            }
        }
        stage('Test') {
            steps {
                dir("${env.WORKSPACE}"){
                    sh('''
                        venv/bin/coverage run -m pytest -v test_*.py \
                            --junitxml=pytest_junit.xml
                    ''')
                }
            }
        }
        stage('Build') {
            steps {
                echo "Build the application in this step..."
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploy the application in this step..."
            }
        }
    }

   post {
        always {
            dir("${env.WORKSPACE}") {
                // Generar reporte XML de cobertura
                sh 'venv/bin/coverage xml'

                // Publicar cobertura visualmente en Jenkins
                publishCoverage adapters: [jacocoAdapter('**/coverage.xml')],
                    sourceFileResolver: sourceFiles('STORE_LAST_BUILD')
            }

            // Publicar reportes de pruebas JUnit
            junit allowEmptyResults: true, testResults: '**/pytest_junit.xml'
            junit allowEmptyResults: true, testResults: '**/pylint_junit.xml'
        }
    }
}
