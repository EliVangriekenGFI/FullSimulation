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
					env.DEPLOY_ENV = 'Test';
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.4';
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
					env.DEPLOY_ENV = 'UAT';
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.4';
				}
			}
		}
		stage('Deploy from master'){
			when{
				branch 'master'
			}
			steps{
				//Running Jenkins on the master will deploy to the production server
				sh 'echo "Deploying to Production"'
				script{
					env.DEPLOY_ENV = 'PROD';
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.4';
				}
			}
		}
		stage('Deploy'){
			steps{
				script{
					echo "Deploy to ${env.DEPLOY_ENV} - ${env.DEPLOY_FRONTEND_ADDR}"
				}
			}
		}
    }
}
