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
            sh 'docker build -f Dockerfile.prod -t mywebapp2:v1.0 .'
        }
      }
    }

    stage("Push to DockerHub ") {
        steps {
            withCredentials([usernamePassword(credentialsId: '24708757-3501-4d51-9709-efecde774cbb', passwordVariable: 'admin', usernameVariable: 'root')]) {
                sh 'docker login -u syrkashev-av@yandex.ru -p Pilot_Jgnbvev_1966'
                sh 'docker tag mywebapp2:v1.0 syrkashevav/mywebapp2:v1.0'
                sh 'docker push syrkashevav/mywebapp2:v1.0'
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
