pipeline {
    agent any

    stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool 'Maven 3.8.1' // Usa el nombre exacto configurado
                    withSonarQubeEnv() {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.host.url=http://${SONARQUBE_URL}:9000 -Dsonar.projectKey=demo-qube -Dsonar.projectName=demo-qube"
                    }
                }
            }
        }

    stages {
        
          stage('SCM') {
            checkout scm
          }
            
          stage('SonarQube Analysis') {
            def mvn = tool 'Default Maven';
            withSonarQubeEnv() {
              sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=con-jenkins -Dsonar.projectName='con-jenkins'"
            }
          }

        stage('Build') {
            steps {
                echo 'Building...'
                // Aquí puedes agregar tus comandos de compilación, por ejemplo:
                // sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // Aquí puedes agregar tus comandos de prueba, por ejemplo:
                // sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Aquí puedes agregar tus comandos de despliegue, por ejemplo:
                // sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
