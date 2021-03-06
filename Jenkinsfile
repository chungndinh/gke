pipeline {
    agent any
	
	environment {
		DOCKER_IMAGE = 'chungnd/devops'
		PROJECT_ID = 'lively-transit-313800'
                CLUSTER_NAME = 'cluster-1'
                LOCATION = 'asia-south1-a'
                CREDENTIALS_ID = 'kubernetes'		
	}
	
    stages {
	    
		stage('Test') {
		    steps {
			    echo "Testing..."
			    sh '/usr/local/bin/phpunit/phpunit --version'
				sh 'pwd'
				sh 'pwd'
				sh '/usr/local/bin/phpunit/phpunit .'
		    }
	    }

	    stage('Build & Push Docker Image') {
			agent { node {label 'master'}}
			environment {
        		DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
      		}
		    steps {
				
				withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
            			sh "docker login -u chungnd -p ${dockerhub}"
				}
				sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} . "
				sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
				script {
          			if (GIT_BRANCH ==~ /.*master.*/) {
						sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
						sh "docker push ${DOCKER_IMAGE}:latest"
					}
				}
				sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
		    }
	    }
	    stage('Deploy to K8s') {
		    steps{
				script {
          			if (GIT_BRANCH ==~ /.*master.*/) {
						echo "Deployment started ..."
						sh 'ls -ltr'
						sh 'pwd'
						
						echo "Start deployment of Deployment.yaml"
						step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'Deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
						echo "Deployment Finished ..."
					  }
					else
					{
						echo 'Only merge Master that deploy in GKE'
					}
				}
			}
			
	    }
    }
}
