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
            steps {
                sh "docker build -t account-service:${env.BUILD_NUMBER} account-service"
                
                sh "docker build -t auth-service${env.BUILD_NUMBER} auth-service"
           
                sh "docker build -t config:${env.BUILD_NUMBER} config"
            
                sh "docker build -t gateway:${env.BUILD_NUMBER} gateway"
            
                sh "docker build -t mongodb:${env.BUILD_NUMBER} mongodb"
           
                sh "docker build -t notification-service:${env.BUILD_NUMBER} notification-service"
         
                sh "docker build -t registry:${env.BUILD_NUMBER} registry"
           
                sh "docker build -t statistics-service:${env.BUILD_NUMBER} statistics-service"
          
                sh "docker build -t turbine-stream-service:${env.BUILD_NUMBER} turbine-stream-service"
            }
           
        }
		stage('Deliver'){
			steps{
				sh 'echo OK'
			}
		}
	}
}
