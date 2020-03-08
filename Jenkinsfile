pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'lithe-booster-262009'
                CLUSTER_NAME = 'kubernetes-cluster'
                LOCATION = 'europe-west1-c'
                CREDENTIALS_ID = 'k8s-cred'		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'		
                }
           }
	   stage('Test') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
                   script {
	/*myimage = docker.build("jagdevops01/devops:${env.BUILD_ID}")*/
	/*	     myimage = docker.build("gcr.io/devopspipelinejag/jagdevops01/devops:${env.BUILD_ID}") */
			   myimage = docker.build("mmk4mmk/devops:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
	/*		   docker.withRegistry('https://gcr.io', 'gcr:gcrcredential') { */
			   docker.withRegistry('https://registry.hub.docker.com', 'Docker') {
                            myimage.push("${env.BUILD_ID}")		
                     }
			   
                   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}
