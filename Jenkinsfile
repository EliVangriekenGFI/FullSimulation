pipeline {
    agent any
	
	parameters {
		booleanParam(defaultValue: false, description: 'Should I run tests?', name: 'runTests')
	}
	
    stages {
        stage('Publish Liferay plugins dependencies') {
		  steps {
			script {
				echo "Publishing plugins"
				/*
				def modules = ['onesto-calc']

				for (int i = 0; i < modules.size(); ++i) {
					sh "./gradlew -x test :${modules[i]}:clean :${modules[i]}:publish"
				}

				// TODO @Eli @Gatien Is this still needed ? If we do the build in one single Gradle command ?
				def osgiModules = ['onesto-cbs-util-api', 'onesto-cbs-facade-crm', 'onesto-cbs-backend-product']

				for (int i = 0; i < osgiModules.size(); ++i) {
					sh "./gradlew -x test :modules:${osgiModules[i]}:clean :modules:${osgiModules[i]}:publish"
				}
				*/
			}
		  }
		}
		stage('Build Liferay plugins and themes') {
			steps {
				sh 'echo "Building plugins and themes"'
				//sh "./gradlew clean build -x test"
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
					
					echo "Deploy to ${env.DEPLOY_ENV} - ${env.DEPLOY_FRONTEND_ADDR}"
					
					/* sh """
					  mkdir -p tmp/deploy
					  rm -f tmp/deploy/*
					  find modules -name be.onesto.*.jar -exec cp {} tmp/deploy \\;
					  find themes -name onesto*.war -exec cp {} tmp/deploy \\;
					  scp -r tmp/deploy/* ${serverAddress}
					  rm -rf tmp/deploy

					  ssh adevleeschauwer@${env.DEPLOY_FRONTEND_ADDR} /opt/liferay/script/install.sh
					""" */
				}
			}
		}
		stage('Deploy from release - Deploy to Test'){
			when{
				expression {BRANCH_NAME ==~ /(release)(\/)(.+)/}
			}
			steps{
				//Running Jenkins on the release branch will deploy to the test and UAT servers.
				sh 'echo "Deploying to test"'
				script{
					env.DEPLOY_ENV = 'Test';
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.4';
					
					echo "Deploy to ${env.DEPLOY_ENV} - ${env.DEPLOY_FRONTEND_ADDR}"
					
					/* sh """
					  mkdir -p tmp/deploy
					  rm -f tmp/deploy/*
					  find modules -name be.onesto.*.jar -exec cp {} tmp/deploy \\;
					  find themes -name onesto*.war -exec cp {} tmp/deploy \\;
					  scp -r tmp/deploy/* ${serverAddress}
					  rm -rf tmp/deploy

					  ssh adevleeschauwer@${env.DEPLOY_FRONTEND_ADDR} /opt/liferay/script/install.sh
					""" */
				}
			}
		}
		stage('Deploy from release - Deploy to UAT'){
			options{
				//For now set for one minute will be higher for real example, for example 3 days.
				timeout(time: 1, unit: 'MINUTES')
			}
			input{
				message "How should I proceed?"
				parameters{
					booleanParam(name: 'DEPLOY_UAT', defaultValue: false, description: 'Should I deploy to UAT')
				}
			}
			when{
				expression {BRANCH_NAME ==~ /(release)(\/)(.+)/}
				environment name: 'DEPLOY_UAT', value: 'true'
			}
			steps{
				//Running Jenkins on the release branch will deploy to the test and UAT servers.
				sh 'echo "Deploying to test"'
				script{
					env.DEPLOY_ENV = 'UAT';
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.36';
					
					echo "Deploy to ${env.DEPLOY_ENV} - ${env.DEPLOY_FRONTEND_ADDR}"
					
					/* sh """
					  mkdir -p tmp/deploy
					  rm -f tmp/deploy/*
					  find modules -name be.onesto.*.jar -exec cp {} tmp/deploy \\;
					  find themes -name onesto*.war -exec cp {} tmp/deploy \\;
					  scp -r tmp/deploy/* ${serverAddress}
					  rm -rf tmp/deploy

					  ssh adevleeschauwer@${env.DEPLOY_FRONTEND_ADDR} /opt/liferay/script/install.sh
					""" */
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
					env.DEPLOY_FRONTEND_ADDR = '10.10.10.666';
					
					echo "Deploy to ${env.DEPLOY_ENV} - ${env.DEPLOY_FRONTEND_ADDR}"
					
					/* sh """
					  mkdir -p tmp/deploy
					  rm -f tmp/deploy/*
					  find modules -name be.onesto.*.jar -exec cp {} tmp/deploy \\;
					  find themes -name onesto*.war -exec cp {} tmp/deploy \\;
					  scp -r tmp/deploy/* ${serverAddress}
					  rm -rf tmp/deploy

					  ssh adevleeschauwer@${env.DEPLOY_FRONTEND_ADDR} /opt/liferay/script/install.sh
					""" */
				}
			}
		}
		stage('Deploy'){
			when{
				expression {BRANCH_NAME ==~ /(release)(\/)(.+)/}
			}
			steps{
				sh 'echo "test"'
			}
		}
    }
}
