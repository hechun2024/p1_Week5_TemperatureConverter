pipeline {
  agent any

  tools {
    maven 'Maven'
  }

  environment {
    // Mac 的 docker 路径
    PATH = "/usr/local/bin:${env.PATH}"

    DOCKERHUB_CREDENTIALS_ID = 'docker-hub-cred'
    DOCKERHUB_REPO = 'hechun202601/p1_week5_temperatureconverter'
    DOCKER_IMAGE_TAG = 'latest'
  }

 stages{

    stage('check'){
        steps {
            git url: 'https://github.com/hechun2024/p1_Week5_TemperatureConverter.git'
       }
    }

    stage('build job: '){
        steps {
          sh 'mvn clean install'
        }
    }

    stage('test'){
        steps {
          sh 'mvn test'
        }
    }

    stage('Report'){
        steps {
             sh 'mvn jacoco:report'
        }
    }

    stage('Publish Test Results') {
           steps {
              junit '**/target/surefire-reports/*.xml'
           }
    }

    stage('Publish Coverage Report') {
            steps {
                recordCoverage tools: [jacoco(pattern: 'target/site/jacoco/jacoco.xml')]
            }
    }

    stage('Build Docker Image') {
        steps {
            script {
                docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
            }
        }
    }

    stage('Push Docker Image to Docker Hub') {
        steps {
            script {
                docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                    docker.image("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}").push()
                }
            }
        }
    }
 }
}