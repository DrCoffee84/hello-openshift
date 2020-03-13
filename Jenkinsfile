pipeline {
	agent {
		label "maven"
	}
	options{
		skipDefaultCheckout()
		disableConcurrentBuilds()
	}
	stages { 
		stage('Checkout'){
			steps{
				checkout(scm)
			}
		}
		stage('Compile') {
			steps {
				sh "mvn package -DskupTests"
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
							// paso va a buscar el buildconfig crado previamente con comando oc new-app 
							//											//  build de la imagen
							openshift.selector("bc", "hello-openshift").startBuild("--from-dir=./target", "--wait=true")
						}
					}
				}
			}
		}
	}
}
