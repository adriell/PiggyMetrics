pipeline {
	agent {
		docker {
			image 'maven:3-alpine'
			args '-v /root/.m2:/root/.m2'
		}
	}
    environment {
        CONFIG_SERVICE_PASSWORD = 'jenkinsjava'
        NOTIFICATION_SERVICE_PASSWORD = 'jenkinsjava'
        STATISTICS_SERVICE_PASSWORD = 'jenkinsjava'
        ACCOUNT_SERVICE_PASSWORD = 'jenkinsjava'
        MONGODB_PASSWORD = 'jenkinsjava'
    }
	stages {
		stage('Build'){
			steps {
				sh 'mvn package -DskipTests'
			}
		}
		stage('Test'){
			steps {
				sh  'mvn test -B -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true'
			}
			post {
                failure {
                    sh 'exit 1'
                }
			}
          
		}
        stage('Package'){
           dir('account-service'){
                docker.build("adriell/account-service:${env.BUILD_NUMBER}")
            }
            dir('auth-service'){
                docker.build("adriell/auth-service:${env.BUILD_NUMBER}")
            }
        }
		stage('Deliver'){
			steps{
				sh 'echo OK'
			}
		}
	}
}
