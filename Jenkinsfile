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
        withCredentials([usernamePassword(credentialsId: '64234792-3d30-44bb-a278-6a420e7a93cc', passwordVariable: 'admin', usernameVariable: 'root')]) {
        sh '''
	          sshpass -p admin  ssh -o "StrictHostKeyChecking=no"  root@178.154.200.198

            script {
                withDockerRegistry(credentialsId: 'a07d152b-80fe-4cc3-a4d4-f219d14f68f8') {
                    sh 'docker pull syrkashevav/mywebapp3:v2.0'
                }
            }
            deploy adapters: [tomcat9(credentialsId: '43bd4659-ebc3-40f1-ba49-1d219980b31d', path: '', url: 'http://178.154.200.198:8080')], contextPath: 'mywebapp:v5.0', war: 'target/*.war'
        '''
        }
      }
    }
  }
}
