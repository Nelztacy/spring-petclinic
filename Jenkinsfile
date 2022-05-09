pipeline {
    environment {
    registry = "swagatam04/spring-petclinic"
    registryCredential = 'dockerhub'
    artifactory-url = "https://swagatamjfrog.jfrog.io/artifactory/"
    user = 'demo'
    password = 'jfrog'
   
    
  }
    agent any
    tools {
        maven "MAVEN"
        jdk "JDK"
    }
    stages {
        stage('Initialize'){
            steps{
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo "M2_HOME = /opt/apache-maven-3.8.2"
                
            }
        }
        stage('Build') {
            steps {
                dir("/var/lib/jenkins/workspace/MAVENBUILD") {
                sh './mvnw package'
               
                }
            }
        }  
        stage('Jfrog Upload') {
             steps {
                dir("/var/lib/jenkins/workspace/MAVENBUILD") {
                sh 'jfrog c add --artifactory-url="https://swagatamjfrog.jfrog.io/artifactory/" --user="demo" --password="AKCp8mYoSbn1VTzSY6TyGvmeem5ugcr5igytHGx2gBa9rQQS4G1CVt1qA18yYZS2Nd6V94b3k"  --interactive="false"'
                 sh 'jfrog rt u "target/spring-petclinic-2.6.0-SNAPSHOT.jar" test --recursive=false --recursive=false'
               
                }
            }
        }  
            
        stage('Test') {
            steps {
                dir("/var/lib/jenkins/workspace/MAVENBUILD") {
                    sh 'mvn test'
                }
            }
        }
            
        
     stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
   stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
   stage('Cleanup Working Directory') {
            steps{
                cleanWs deleteDirs: true
            }
        }
  }
}
