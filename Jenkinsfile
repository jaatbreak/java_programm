pipeline{
	
	agent {
		label "dev"
		}
		stages{
			stage ("Pull the code from SCM"){
				steps {
					git branch: 'main', url: 'https://github.com/jaatbreak/java_programm.git'
					}
				}
			stage (" Build the code "){
				steps {
					sh 'sudo mvn dependency:purge-local-repository'
					sh 'sudo mvn clean package'
					}
				}
			stage (" Build the image "){
				steps {
					sh 'sudo docker build -t java-repo:$BUILD_TAG .'
					sh 'sudo docker tag java-repo:$BUILD_TAG amansingh12/java-app:$BUILD_TAG'
					}
				}
			stage ( " push the image "){
				steps {
				withCredentials([string(credentialsId: 'docker_hub', variable: 'docker_var')]){
					sh 'sudo docker login -u amansingh12 -p $docker_var'
					sh 'sudo docker push amansingh12/java-app:$BUILD_TAG'
						}
					}
				}
			stage("QAT Testing") {
				steps {
					script {
						sh 'sudo docker run -dit -p :8080 amansingh12/java-app:$BUILD_TAG'
						}
					}
				}
			stage("testing website") {
				steps {
					sh 'echo "The App is ready for the Production"  '
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
				agent{
			            label "deploy"
				}
				steps {
					
			    	 	sh 'sudo kubectl run c1 image=amansingh12/java-app:$BUILD_TAG'
					sh 'sudo kubectl expose pod c1 image==amansingh12/java-app:$BUILD --type=NodePort --port=8080 '
			}
		}
	     }
			
          }

