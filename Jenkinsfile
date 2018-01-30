pipeline {
    agent any
	
	parameters {
		booleanParam(defaultValue: false, description: 'Should I run tests?', name: 'runTests')
	}
	
	environment {
        CI = 'true'
    }
	
    stages {
        stage('Build') {
            steps {
                //Here we can build the project by calling graddle for example
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
		stage('Deploy for develop'){
			when{
				branch 'develop'
			}
			steps{
				//Only deploy to the test server
				sh 'echo "Deploying to the test server"'
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
				sh 'echo "releaseBeforeScript: ${RELEASE}"' 
				script{
					print "release: ${RELEASE}"
					print "version: ${VERSION}"
					if(RELEASE == "true"){
						sh 'echo push to the release branch'
						sh 'echo "The pushed version is ${VERSION}"'
						sh 'git checkout develop'
						sh 'git pull origin develop'
					}else{
						sh 'echo "Not creating release"'
					}
				}
			}
		}
		stage('Deploy for release'){
			when{
				branch 'release'
			}
			steps{
				//Deploy to all servers
				sh 'echo "Deploying to all servers"'
			}
		}
    }
}
