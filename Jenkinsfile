pipeline {
  agent {
    docker {
        image 'syrkashevav/boxfuse:v1.0'
        args '-v /var/run/docker.sock:/var/run/docker.sock'
      }
    }

  environment {
    NEXUS_URL = "178.154.221.16:8123"
    PRODE = "178.154.200.198"
    USERNAME = "admin"
    PASSWORD = "admin"
  }

  tools {
          maven 'm3'
  }

  stages {

    stage('git clone builder') {
      steps {
        sh 'rm -rf boxfuse-sample-java-war-hello'
        sh 'git clone https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        dir("boxfuse-sample-java-war-hello") {
            sh 'mvn package'
            sh 'pwd'
            sh 'ls -la'
        }
      }
    }

    stage('Build dockerfile, push to Nexus and run docker') {
      steps {
          git branch: 'main', credentialsId: '24708757-3501-4d51-9709-efecde774cbb', url: 'https://github.com/SyrkashevAV/jenkins-pipeline.git'
            dir('/var/lib/jenkins/workspace/Docker-build-pipeline') {
            sh 'echo 555555'
            sh 'pwd'
            sh 'ls -la'
            sh 'docker build -t mywebapp:v5.0 /var/lib/jenkins/workspace/Docker-build-pipeline/dockerfile.prod'
          }
      }
    }

    stage("Login, push to Nexus ") {
        steps {
                withCredentials([usernamePassword(credentialsId: '2bf3b32f-9aef-4dad-ae85-7aaecbb9c4c9', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                sh 'docker login ${NEXUS_URL} -u $USERNAME -p $PASSWORD'

                sh 'docker tag mywebapp:v5.0 ${NEXUS_URL}/mywebapp:v5.0'
                sh 'docker push ${NEXUS_URL}/mywebapp:v5.0'
                sh 'docker run -d -p 8080:8080 mywebapp:v5.0'
              }
        }
    }

    stage('Deploy to Production') {
      steps {
            deploy adapters: [tomcat9(credentialsId: '43bd4659-ebc3-40f1-ba49-1d219980b31d', path: '', url: 'http://178.154.200.198:8080')], contextPath: 'mywebapp:v5.0', war: 'target/*.war'
      }
    }
 }
}
