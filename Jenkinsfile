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
                sh '''
                docker build -t parcial3-app:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Run Container (Test)') {
            steps {
                sh '''
                docker rm -f parcial3-test || true
                docker run -d --name parcial3-test -p 5000:5000 parcial3-app:${BUILD_NUMBER}
                sleep 10
                '''
            }
        }

        stage('Security Scan (ZAP Baseline)') {
            steps {
                sh '''
                docker run --rm \
                  -v $PWD:/zap/wrk \
                  owasp/zap2docker-stable zap-baseline.py \
                  -t http://host.docker.internal:5000 \
                  -r zap_report.html || true
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            sh '''
            docker rm -f parcial3-test || true
            '''
        }
    }
}
