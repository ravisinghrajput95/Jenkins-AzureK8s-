pipeline{
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
    }
    
    stages {
        stage("Code Checkout") {
            steps {
                echo "========Checking latest code for Build & Release========"
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ravisinghrajput95/Jenkins-AzureK8s-.git'
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
            echo "========Copy artifact========"
            sh 'pwd'
		    sh 'cp -r target/*.jar docker'
           }
        }

        stage("Docker build and Push") {
            steps {
                echo "========Building the Docker image========"
                script {         
                   def customImage = docker.build('petclinic/spring', "./docker")
                   docker.withRegistry('https://petclinicspring.azurecr.io', 'azure-registry') {
                   customImage.push("$BUILD_NUMBER")
                }  
            }
        }
    }
        stage("Teardown"){
            steps{
                echo "Removing image after pushed to Azure registry"
                sh '''
                  docker rmi petclinicspring.azurecr.io/petclinic/spring:$BUILD_NUMBER
                  docker image prune -f
                '''
            }
        }
        
        stage('Deployment to Azure K8s cluster') {
            steps {           
                sh 'pwd'
                sh 'cp -R helm/* .'
		        sh 'ls -ltr'
                sh 'pwd'
                sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic  --set image.repository=petclinicspring.azurecr.io/petclinic/spring --set image.tag=$BUILD_NUMBER'
              			
            }           
        }
        
    }
}
