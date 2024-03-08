pipeline {
  agent {
    docker {
        image 'syrkashevav/boxfuse:v1.0'
        args '-v /var/run/docker.sock:/var/run/docker.sock'
      }
    }

  tools {
          maven 'm3'
  }

  stages {

    stage('git clone builder') {
      steps {
        sh 'rm -rf jenkins-pipeline'
        sh 'git clone https://github.com/SyrkashevAV/jenkins-pipeline.git'
        dir('jenkins-pipeline') {
            sh 'mvn package'
        }
      }
    }

    stage('Build dockerfile, push to Nexus and run docker') {
      steps {
        dir('jenkins-pipeline') {
            sh 'docker build -f Dockerfile.prod -t mywebapp3:v2.0 .'
        }
      }
    }

    stage('Push to DockerHub') {
        steps {
            script {
                    withDockerRegistry(credentialsId: 'a07d152b-80fe-4cc3-a4d4-f219d14f68f8') {
                      sh 'docker tag mywebapp3:v2.0 syrkashevav/mywebapp3:v2.0'
                      sh 'docker push syrkashevav/mywebapp3:v2.0'
                    }
            }
        }
    }

    stage('Deploy to Production') {
      steps {
            sshagent(['b6aef2a9-dc31-4520-ba25-efc9cfa61070']) {
            sh 'docker pull syrkashevav/mywebapp3:v2.0'
            }
      }
    }
  }
}
