pipeline {
    agent any

    environment {
        //configuración de la herramienta
        scannerHome = tool 'Sonar Scanner'
    }

    stages {
        
        stage('Build') {
            steps {
                echo 'Building...'
                // Aquí puedes agregar tus comandos de compilación, por ejemplo:
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                // Aquí puedes agregar tus comandos de prueba, por ejemplo:
                sh 'mvn test'
            }
        }

        stage('Sonar Analysis') {
            steps {
                echo 'Sonar Analysis'
                withSonarQubeEnv('Sonar') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=demo-qube -Dsonar.sources=. -Dsonar.host.url=http://${SONARQUBE_URL}:9000 -Dsonar.junit.reportsPath=target/surefire-reports/ -Dsonar.jacoco.reportsPath=target/jacoco.exec -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                sleep(10)
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }


        stage('Upload Artifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '192.168.1.90:8081',
                    groupId: 'QA',
                    version: """${env.BUILD_ID}-${env.BUILD_TIMESTAMP}""",
                    repository: 'td-maven-repo',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'webApp',
                        classifier: '',
                        file: 'target/demo-app.war',
                        type: 'war'],
                    ]
                )
            }
        }
    } 

    post {
        always {
            script {
                def COLOR_MAP = [
                    'SUCCESS': 'good',
                    'FAILURE': 'danger'
                ]
                def resultColor = COLOR_MAP[currentBuild.currentResult.toString()]
                slackSend(
                    channel: '#notificaciones', 
                    color: resultColor, 
                    message: "**${currentBuild.currentResult}**: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\nMore Info at: ${env.BUILD_URL}"
                )
            }
        }
    }
}  
