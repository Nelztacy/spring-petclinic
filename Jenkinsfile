pipeline {
    environment {
    registry = "swagatam04/spring-petclinic"
    registryCredential = 'dockerhub'
    AF_BASE_URL= 'https://artifactory.rosetta.ericssondevops.com/artifactory/sd-mana-att-sds-maven/att_swan/EOC'
    AF_API_TOKEN='AKCp5e2gHgqTxm9hZav5tNBoqsPPhee5ZrhpEiiRuxdLWVd9yZfLmj3snLfpXkQDPHpppaHXg'
    
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
                sh 'jfrog rt c --url="$AF_BASE_URL" --apikey="$AF_API_TOKEN"'
                 sh 'jfrog rt u "target/spring-petclinic-2.6.0-SNAPSHOT.jar" "$AF_BASE_URL/spring-petclinic-2.6.0-SNAPSHOT-$BUILD_NUMBER.jar" --recursive=false'
               
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
