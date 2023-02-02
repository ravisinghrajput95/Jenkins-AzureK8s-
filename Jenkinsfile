pipeline{
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
    }
    
    stages {
        stage("Code Checkout") {
            steps {
                echo "========Checking latest code for Build & Release========"
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ravisinghrajput95/Jenkins-CICD-Azure-K8.git'
            }
        }

        stage("Test") {
            steps {
                echo "========Unit tests execution in progress========"
                sh 'mvn test'
            }
        }

        stage("Compile") {
            steps {
                echo "========Code compilation in progress========"
                sh 'mvn package'
            }
        }

        stage('Copy Artifact') {
           steps { 
                   sh 'pwd'
		   sh 'cp -r target/*.jar docker'
           }
        }

        stage("Docker build and Push") {
            steps {
                echo "========Building the Docker image========"
                script {         
                   def customImage = docker.build('petclinic/spring', "./docker")
                   docker.withRegistry('https://petclinicspring.azurecr.io', 'acr-demo') {
                   customImage.push("${env.BUILD_NUMBER}")
                }  
            }
        }
    }
        
    }
}
