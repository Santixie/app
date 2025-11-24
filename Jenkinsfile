pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image (simulado)') {
            steps {
                sh '''
                echo "Compilando y construyendo imagen Docker de la aplicación..."
                echo "docker build -t parcial3-app:${BUILD_NUMBER} ."
                '''
            }
        }

        stage('Run Container (Test) - simulado') {
            steps {
                sh '''
                echo "Levantando contenedor de prueba..."
                echo "docker run -d --name parcial3-test -p 5000:5000 parcial3-app:${BUILD_NUMBER}"
                echo "Ejecutando pruebas básicas contra http://localhost:5000/login"
                '''
            }
        }

        stage('Security Scan (ZAP Baseline - simulado)') {
            steps {
                sh '''
                echo "Ejecutando OWASP ZAP Baseline Scan contra la aplicación..."
                echo "docker run --rm owasp/zap2docker-stable zap-baseline.py -t http://app:5000 -r zap_report.html"
                echo "<html><body><h1>Reporte simulado de ZAP</h1><p>En el entorno real, aquí se adjuntaría el informe HTML generado por ZAP.</p></body></html>" > zap_report.html
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
        }
    }
}
