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
            recordCoverage tools: [
                coverage(
                    tool: jacoco(),
                    pattern: 'target/site/jacoco/jacoco.xml'
                )
            ]
        }
    }

    stage('Build Docker Image') {
        steps {
            sh """
            /usr/local/bin/docker build -t ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG} .
            """
        }
    }

    stage('Push Docker Image to Docker Hub') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: DOCKERHUB_CREDENTIALS_ID,
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh """
                echo $DOCKER_PASS | /usr/local/bin/docker login -u $DOCKER_USER --password-stdin
                /usr/local/bin/docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}
                """
            }
        }
    }
 }
}
