pipeline {
    agent any
    
    stages {
        // Step 1
        stage('SCM') {
                steps {
                    git 'https://github.com/SubhashchandraK/demoCICD.git'
                }        
        }

        
  stage('SonarQube analysis') {
      steps {
    withSonarQubeEnv(credentialsId: 'e3c72120-75bb-4ff4-ba63-eff221d6f364', installationName: 'sonar') { // You can override the credential to be used, If you have configured more than one global server connection, you can specify the corresponding SonarQube installation name configured in Jenkins
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
    }
      }
    }
        // Step 2
        stage('Build by Maven') {
                steps {
                    sh 'mvn clean package'
                }
        }
        
        // Step 3
        stage('Build docker image') {
                steps {
                    sh "sudo docker build -t javaapp:${BUILD_NUMBER} ."
                }
        }

        stage('scan docker image') {
                steps {
                  sh "trivy image javaapp:${BUILD_NUMBER}"
                }
        }
        // Step 4
        stage('Push docker image') {
                steps {
                  //  withCredentials([string(credentialsId: 'Docker_hub_password', variable: 'VAR_FOR_DOCKERPASS')]) {
                    //sh "sudo docker login -u webdevprashant -p $VAR_FOR_DOCKERPASS"
                    //}
                  //  sh "sudo docker push webdevprashant/javaapp:${BUILD_NUMBER}"
               
                sh 'echo pushed image to docker hub'
             }
        }
        
        // Step 5 
        stage('Deploy Java App in  Dev Env') {
                steps {
                       //sh "sudo docker rm -f myjavaappdevenv"
                        //sh "sudo docker run  -d -p 1222:8080 --name myjavaappdevenv webdevprashant/javaapp:${BUILD_NUMBER}"
                         sh "sudo docker run  -d -p 1222:8080 --name myjavaappdevenv${BUILD_NUMBER} javaapp:${BUILD_NUMBER}"
                }
        }
        
        // Step 6  in  Redhat CLI 1 
        stage('Deploy Java in QA/Test Env') {
            steps {
                    // sshagent(['QA_ENV_SSH_CRED']) {
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"
            //sh "sudo docker rm -f myjavaappqatestenv"            
            sh "sudo docker run  -d -p 1223:8080 --name myjavaappqatestenv${BUILD_NUMBER} javaapp:${BUILD_NUMBER}"           
                    // }
            }
        }
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://0.0.0.0:1223/java-web-app/'
                }   
            }
        }
        
        // Step  in Redhat 8 CLI 2
        stage('Deploy webAPP in Prod Env') {
            steps {
               
            //   sshagent(['QA_ENV_SSH_CRED']) {                    
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"                   
                // }
               // sh "sudo docker rm -f myjavaappprodenv"
                sh "sudo docker run  -d -p 1224:8080 --name myjavaappprodenv${BUILD_NUMBER} javaapp:${BUILD_NUMBER}"  
            }
        }
    }
}      
