pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/vishwanath0303/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
    
        
         stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
        stage("PUSH TO REPO "){
            steps{
                withCredentials([usernamePassword(credentialsId:"docker-cred",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass} "
                    sh "docker tag node-app-demo  ${env.dockerHubUser}/Petclinic:latest"
                    sh "docker push ${env.dockerHubUser}/Petclinic:latest"
                } 
            }
        }
         stage("Deploy "){
            steps{
                sh "docker run --name petclinic -d -p 8090:8090 Petclinic:latest "
            }
        }
    }
}
