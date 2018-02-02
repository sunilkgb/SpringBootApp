pipeline {
    agent any
    tools { 
        maven 'Maven_3.2.5' 
        jdk 'JDK8_131' 
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
stage ('Checkout') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/reddymh/SpringBootApp.git']]])
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean package -DaltDeploymentRepository=nexus-releases::default::http://nexus:8081/repository/totaldevops_snapshot'
            }
        }
        stage ('Sonar'){
            steps{
                sh 'mvn sonar:sonar -Dsonar.host.url=http://sonar:9000 -Dsonar.login=${SONAR_USER_TOKEN_ID} -DaltDeploymentRepository=nexus-releases::default::http://nexus:8081/repository/totaldevops_snapshot'
            }
            
        }
        stage ('Deploy To Dev'){
            steps{
                sh 'nohup java -jar target/docker-spring-boot.jar > log.out &'
            }
            
        }
		stage ('Publish Artifacts') {
            steps {
                sh 'mvn clean deploy -DaltDeploymentRepository=nexus-releases::default::http://nexus:8081/repository/totaldevops_snapshot'
            }
        }
        
    }
}