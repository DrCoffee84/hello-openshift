pipeline {
	agent {
		label "maven"
	}
	stages { 
		stage('Checkout') {
			steps {
				sg "mvn package -DskupTests"
			}
		}
		stage('Test') {
			steps {
				sh "mvn test"
			}
		}
		stage('Build Image') {
			steps {
				script{
					openshift.withCluster{
						openshift.withProject{
							openshift.selector("bc", "hello-openshift").startBuild("--wait=true")
						}
					}
				}
			}
		}
	}
}
