#!groovy
//teste

node {
    withEnv([
        "CONFIG_SERVICE_PASSWORD=jenkinsjava",
        "NOTIFICATION_SERVICE_PASSWORD=jenkinsjava",
        "STATISTICS_SERVICE_PASSWORD=jenkinsjava",
        "ACCOUNT_SERVICE_PASSWORD=jenkinsjava",
        "MONGODB_PASSWORD=jenkinsjava"
    ]){}

    stage('Build Maven') {
        docker.image('maven:3-alpine').inside {
            sh 'mvn package -DskipTests'
        }
    }

    stage('Unit Tests') {
        docker.image('maven:3-alpine').inside {
            sh 'mvn test -B -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true'
        }
    }
    stage('Static Analysis'){
        docker.image('maven:3-alpine').inside {
            sh 'mvn sonar:sonar -Dsonar.host.url=http://192.168.15.13:9000  -Dsonar.login=90a78d989174323f6b3b4e59f0f6e2ef4efb69f7'
        }
    }
    stage('Build Image'){
        sh 'echo OK'
        docker.withRegistry('https://192.168.15.13:8123'){
            
            def accountService = docker.build("account-service:latest" , "./account-service")
            accountService.push();
            
            def authService = docker.build("auth-service:latest" , "./auth-service")
            authService.push()
            
            def config = docker.build("config:latest" , "./config")
            config.push()
            
            def gateway = docker.build("gateway:latest" , "./gateway")
            gateway.push()''
            
            def mongodb = docker.build("mongodb:latest" , "./mongodb")
            mongodb.push()
            
            def notification = docker.build("notification-service:latest" , "./notification-service")
            notification.push()
            
            def registry = docker.build("registry:latest" , "./registry")
            registry.push()
            
            def statisticsService = docker.build("statistcs-service:latest" , "./statistics-service")
            statisticsService.push()
            
            def turbineStreamService = docker.build("turbine-stream-service:latest" , "./turbine-stream-service")
            turbineStreamService.push()
        }
    }
    stage('Approval') {
        timeout(time:5, unit:'MINUTES') {
            input 'Do I have your approval for deployment?'
        }
    } 
   
    stage('Deploy'){
          sh 'ansible-playbook -u root ansible/playbook.yml -e host_key_checking=False -i ansible/inventory/inventory.ini'
    }
}

