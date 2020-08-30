pipeline {
        agent any
        environment {
            registry = "jijjoe001/sprint6dockerjenkinsgcloud"
            registryCredential = 'jijjoe001'
            dockerImage = ''
		PROJECT_ID = 'savvy-mote-287309'
 		CLUSTER_NAME = 'sprint6-kubectl-cluster-glcoud'
 		LOCATION = 'us-east4-a'
 		CREDENTIALS_ID = 'jijoekube'
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
	                      dockerImage = docker.build registry + ":$BUILD_NUMBER"
	                   }
	                }
		   }
            stage('Deploy Image') {
                steps{
                    script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                    }
                }
            }
	           stage('Deploy to GKE') {
 			steps{
 				echo "Deployment started"
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' Deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID,
				      clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'Deployment.yaml',
				      credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished"
 	            }
	          }
	    }
	}
