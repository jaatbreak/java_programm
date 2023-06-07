pipeline{
	
	agent {
		label "slave-1"
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
						sh 'sudo docker run -dit -p 8080:8080 amansingh12/java-app:$BUILD_TAG'
						}
					}
				}
			stage("testing website") {
				steps {
					retry(5) {
						script {
							sh 'curl --silent http://43.205.124.201:8080/java-web-app/ | grep -i "india" > /home/ubuntu/test.txt'
							}
						}
					}
				}
			}
		
}
