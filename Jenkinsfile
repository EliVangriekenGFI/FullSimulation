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
					booleanParam(name: 'CREATE_RELEASE', defaultValue: false, description: 'Is this the first setup?')
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
						writeFile file: "version.txt", text: "${VERSION}"
						sh 'git stage .'
						sh 'git commit -m "created release ${VERSION}"'
						sh 'git push'
						if(CREATE_RELEASE == "true"){
							sh 'git branch release'
							sh 'git push --set-upstream origin release'
						}
						sh 'git checkout release'
						sh 'git pull . develop'
						sh 'git stage .'
						//sh 'git commit -m "created release ${VERSION}"'
						sh 'git push'
					}else{
						sh 'echo "Not creating release"'
					}
				}
			}
		}
		stage('Deploy for release'){
			input{
				message "How should I proceed?"
				parameters{
					booleanParam(name: 'FINISH', defaultValue: false, description: 'Should I finish this release?')
				}
			}
			when{
				branch 'release'
			}
			steps{
				//Deploy to all servers
				sh 'echo "Deploying to all servers"'
				script{
					sh 'git checkout release'
					sh 'git pull origin release'
					def version = readFile "version.txt"
					print "the version is ${version}"
					sh "git branch"
					/*sh 'git checkout master'
					sh 'git pull origin master'
					sh 'git pull . release'
					sh 'git tag version'
					sh 'git push'
					sh 'git push origin' version
					sh 'git checkout release' */
				}
			}
		}
    }
}
