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
				environment name: 'runTests', value: true
			}
			steps{
				//Here we can call our defined test cases, separating them can be beneficial
				sh 'echo "Running tests"'
			}
		}
		stage('Deploy for development'){
			when{
				branch 'development'
			}
			steps{
				//Only deploy to the test server
				sh 'echo "Deploying to the test server"'
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
