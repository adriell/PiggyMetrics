#!groovy


node {
     environment {
        CONFIG_SERVICE_PASSWORD = 'jenkinsjava'
        NOTIFICATION_SERVICE_PASSWORD = 'jenkinsjava'
        STATISTICS_SERVICE_PASSWORD = 'jenkinsjava'
        ACCOUNT_SERVICE_PASSWORD = 'jenkinsjava'
        MONGODB_PASSWORD = 'jenkinsjava'
    }
    /* Requires the Docker Pipeline plugin to be installed */

    stage('Build Maven') {
        docker.image('maven:3-alpine').inside {
            sh 'mvn package -DskipTests'
        }
    }

    stage('Tests') {
        docker.image('maven:3-alpine').inside {
            sh 'mvn test -B -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true'
        }
    }
    stage('Build Docker'){
        docker.withRegistry('https://192.168.15.13:8123'){
            
            def accountService = docker.build("account-service:${env.BUILD_ID}" , "./account-service")
            accountService.push();
            
            def authService = docker.build("auth-service:${env.BUILD_ID}" , "./auth-service")
            authService.push()
            
            def config = docker.build("config:${env.BUILD_ID}" , "./config")
            config.push()
            
            def gateway = docker.build("gateway:${env.BUILD_ID}" , "./gateway")
            gateway.push()
            
            def mongodb = docker.build("mongodb:${env.BUILD_ID}" , "./mongodb")
            mongodb.push()
            
            def notification = docker.build("notification-service:${env.BUILD_ID}" , "./notification-service")
            notification.push()
            
            def registry = docker.build("registry:${env.BUILD_ID}" , "./registry")
            registry.push()
            
            def statisticsService = docker.build("statistcs-service:${env.BUILD_ID}" , "./statistics-service")
            statisticsService.push()
            
            def turbineStreamService = docker.build("turbine-stream-service:${env.BUILD_ID}" , "./turbine-stream-service")
            turbineStreamService.push()
        }
     }
    stage('Sonar'){
          sh "echo sonar "
    }
    stage('Deploy'){
          sh 'ansible-playbook -u root /home/vagrant/ansible/playbook.yml  -i hosts'
    }
}

