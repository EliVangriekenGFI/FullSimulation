pipeline {
    agent any
	
	parameters {
		booleanParam(defaultValue: false, description: 'Should I run tests?', name: 'runTests')
	}
	
    stages {
        stage('Build') {
			steps {
                //Here we can build the project by calling gradle for example
				sh 'echo "Building the projects"'
            }
        }
		stage('Test'){
			when{
				environment name: 'runTests', value: 'true'
			}
			steps{
				//Here we can call our defined test cases, separating them can be beneficial
				sh 'echo "Running tests"'
			}
		}
		stage('Deploy from develop'){
			when{
				branch 'develop'
			}
			steps{
				//Running Jenkins on the develop branch will only deploy on the test server
				script{
					//This ensures that all branches are visible.
					//Fetch also needs to be configured properly in the git folder of the Jenkins workspace.
					sh 'git checkout develop'
					sh 'git fetch'
					def version = readFile "version.txt"
					print "Deploying version ${version} to the test server"
					writeFile file: "/.git/test.txt", text: "test"
				}
			}
		}
		stage('Create release from develop'){
			input{
				message "How should I proceed?"
				parameters{
					booleanParam(name: 'RELEASE', defaultValue: false, description: 'Should I create a release?')
					string(name: 'VERSION', defaultValue: '1.0.0', description: 'What is the version number?')
				}
			}
			when{
				branch 'develop'
			}
			steps{
				script{
					if(RELEASE == "true"){
						sh 'git checkout develop'
						sh 'git pull origin develop'
						writeFile file: "version.txt", text: "${VERSION}"
						sh 'git stage .'
						sh 'git commit -m "created release ${VERSION}"'
						sh 'git push'
						sh 'git checkout release'
						sh 'git pull . develop'
						sh 'git stage .'
						sh 'git push'
						print "Created release version ${VERSION} on the release branch"
					}else{
						sh 'echo "Not creating release"'
					}
				}
			}
		}
		stage('Deploy from release'){
			when{
				branch 'release'
			}
			steps{
				//Running Jenkins on the release branch will deploy to the test and UAT servers.
				sh 'echo "Deploying to test and UAT"'
				script{
					//This ensures that all branches are visible.
					sh 'git checkout release'
					sh 'git fetch'
				}
			}
		}
		stage('Release for production'){
			options{
				//For now set for one minute will be higher for real example, for example 3 days.
				timeout(time: 1, unit: 'MINUTES')
			}
			input{
				message "How should I proceed?"
				parameters{
					booleanParam(name: 'FINISH', defaultValue: false, description: 'Should I finish this release for production?')
				}
			}
			when{
				branch 'release'
			}
			steps{
				//The user will be prompted to finish the release for production (push to the master branch).
				//Depending on the results of the previous step the user might want to decline.
				//A timeout will ensure that the pipeline will not stay alive forever while waiting on a response of the user.
				//The release on the master will also be tagged with the version number
				script{
					if(FINISH == "true"){
						sh 'git checkout release'
						sh 'git pull origin release'
						def version = readFile "version.txt"
						sh 'git checkout master'
						sh 'git pull origin master'
						sh 'git pull . release'
						sh "git tag ${version}"
						sh 'git push'
						sh "git push origin ${version}"
						sh 'git checkout release'
						print "Version ${version} released for production"
					}else{
						print "No release for production"
					}
				}
			}
		}
		stage('Deploy from master'){
			when{
				branch 'master'
			}
			steps{
				script{
					//This ensures that all branches are visible.
					sh 'git checkout master'
					sh 'git pull origin master'
					sh 'git fetch'
					//Deploying from master will deploy to the production server.
					def version = readFile "version.txt"
					print "Deploying version ${version} to production"
				}
			}
		}
    }
}
