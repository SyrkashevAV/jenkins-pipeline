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
              sshagent(['8d6463fc-c7ca-4ca9-95ad-4179b8edaf2d']) {
                withCredentials([sshUserPrivateKey(credentialsId: '254a11ec-98c5-492f-882a-25c4bd6375c1', keyFileVariable: 'keyfile', passphraseVariable: 'admin', usernameVariable: 'root')]) {
                sh '''
                  mkdir -p ~/.ssh && ssh-keyscan -H 158.160.36.155 >> ~/.ssh/known_hosts
                  scp -vvv -o StrictHostKeyChecking=no ~/.ssh/id_rsa jenkins@158.160.36.155:~/.ssh/id_rsa
                  ls -la ~/.ssh
                  cat ~/.ssh/known_hosts
                  docker pull syrkashevav/mywebapp3:v2.0
                  '''
                }
              }
      }
    }
  }
}
