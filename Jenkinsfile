pipeline {
	agent any
	stages {
		stage("SCM pushing") {
			steps {
				git 'https://github.com/gouravaas/java_docker_app.git'
				}
			}

		stage("build") {
			steps {
				sh 'sudo mvn dependency:purge-local-repository'
				sh 'sudo mvn clean package'
				}
			}
		stage("Image") {
			steps {
				sh 'sudo docker build -t java-repo:$BUILD_TAG .'
				sh 'sudo docker tag java-repo:$BUILD_TAG Gouravaas/pipeline-java:$BUILD_TAG'
				}
			}
				
	
		stage("Docker Hub") {
			steps {
			withCredentials([string(credentialsId: 'docker_hub_passwd', variable: 'docker_hub_password_var')]) {
				sh 'sudo docker login -u Gouravaas -p ${docker_hub_password_var}'
				sh 'sudo docker push Gouravaas/pipeline-java:$BUILD_TAG'
				}
			}	

		}
		stage("QAT Testing") {
			steps {
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
				sh 'sudo docker run -dit -p 8080:8080  Gouravaas/pipeline-java:$BUILD_TAG'
				}
			}
		stage("testing website") {
			steps {
				retry(5) {
				sh 'curl --silent http://65.2.140.187:8080/java-web-app/ | grep -i "india" '
					}
				}
			}

		stage("Approval status") {
			steps {
				script {
					 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
					}
				}	
		}
		stage("Prod Env") {
			steps {
			 sshagent(['ubuntu']) {
			    sh 'ssh -o StrictHostKeyChecking=no ubuntu@65.2.140.187 sudo docker rm -f $(sudo docker ps -a -q)' 
	                    sh "ssh -o StrictHostKeyChecking=no ubuntu@65.2.140.187 sudo docker run  -d  -p  49153:8080  Gouravaas/javatest-app:$BUILD_TAG"
				}
			}
		}
	}
}

