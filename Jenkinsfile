pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build -t parcial3-app:%BUILD_NUMBER% .
                '''
            }
        }

        stage('Run Container (Test)') {
            steps {
                bat '''
                docker rm -f parcial3-test 2>NUL || echo No previous container
                docker run -d --name parcial3-test -p 5000:5000 parcial3-app:%BUILD_NUMBER%
                '''
                // pequeña pausa para que la app arranque
                sleep(time: 10, unit: 'SECONDS')
            }
        }

        stage('Security Scan (ZAP Baseline)') {
            steps {
                bat '''
                docker run --rm ^
                  -v %CD%:/zap/wrk ^
                  owasp/zap2docker-stable zap-baseline.py ^
                  -t http://host.docker.internal:5000 ^
                  -r zap_report.html || echo ZAP finalizado con advertencias
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            // apagar contenedor de pruebas
            bat 'docker rm -f parcial3-test 2>NUL || echo Container already removed'
        }
    }
}
