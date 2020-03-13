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
							env.TAG = readMavenPom().getVersion()
							// paso va a buscar el buildconfig crado previamente con comando oc new-app 
							//											//  build de la imagen
							openshift.selector("bc", "hello-openshift").startBuild("--from-dir=./target", "--wait=true")

							openshift.tag("hello-openshift:latest","hello-openshift:${evn.TAG}")
						}
					}
				}
			}
		}
		stage('Deploy Image') {
			steps {
				script{
					openshift.withCluster{
						openshift.withProject{
							openshift.set("triggers","dc/hello-openshift","--remove-all")
							openshift.set("triggers","dc/hello-openshift","--from-image=hello-openshift:${evn.TAG}","-c hello-openshift")
							openshift.selector("dc","hello-openshift").rollout().status()
						}
					}
				}
			}
		}
		stage('Promote Stage') {
			steps {
				input("promote to Stage?")
				script{
					openshift.withCluster{
						openshift.withProject{
							openshift.tag("hello-openshift:${evn.TAG}","hello-openshift-stage/hello-openshift:${evn.TAG}")
						}
					}
				}
			}
		}
		stage("Deploy Stage"){
			script{
				openshift.withCluster{
					openshift.withProject("hello-openshift-stage"){
						openshift.set("trigggers","dc/hello-openshift","--remove-all")
						openshift.set("trigggers","dc/hello-openshift","--form-image=hello-openshift:${TAG}","-c hello-openshift")
						openshift.selector("dc","hello-openshift").rollout().status()
					}
				}
			}
		}
	}
}
