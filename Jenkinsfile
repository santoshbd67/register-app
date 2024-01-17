pipeline {
    agent any
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
        
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/santoshbd67/register-app.git'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
         stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=register \
                         -Dsonar.java.binaries=. \
                         -Dsonar.projectKey=register '''
               }
            }
        }
        stage('build and push docker images') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'dockerhub') {
                       sh "docker build -t register ."
                       sh "docker tag register santoshbd67/register:latest"
                       sh "docker push santoshbd67/register:latest"
                 }
               }
            }
        }
         stage("Trivy Image Scan") {
             steps {
                 script {
	              sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image santoshbd67/register:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table > trivyimage.txt')
                 }
             }
         }
      }      
   }

