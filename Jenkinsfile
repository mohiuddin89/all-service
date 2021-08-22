pipeline {
  agent any
 
  options {
        timeout(time: 1, unit: 'HOURS')
    }
 
  stages {
    stage('Source') {
      steps {
        echo 'Pulling from GitHub'
      }
    }
   
    stage('Build') {
      steps {
        echo '********STARTING BUILD STAGE*******'
        echo '***********************************'
        sh '''
        cd auth
        docker build -t mdmohiuddin/auth:01 .
        cd ../user
        docker build -t mdmohiuddin/user:01 .
        cd ../task
        docker build -t mdmohiuddin/task:01 .
        cd ../attendance
        docker build -t mdmohiuddin/attendance:01 .
        cd ../delivery
        docker build -t mdmohiuddin/delivery:01 .
        cd ../admin
        docker build -t mdmohiuddin/admin:01 .
        '''
        echo '***** DOCKER HUB *****'
        withCredentials([usernamePassword(credentialsId: 'DockerHubC', passwordVariable: 'DHPASS', usernameVariable: 'DHUSER')]) {
          sh "docker login -u ${DHUSER} -p ${DHPASS}"
        }
        echo '***** PUSHING IMAGES TO DOCKER HUB *****'
        sh '''
        docker push mdmohiuddin/auth:01
        docker push mdmohiuddin/user:01
        docker push mdmohiuddin/task:01
        docker push mdmohiuddin/attendance:01
        docker push mdmohiuddin/delivery:01
        docker push mdmohiuddin/admin:01
        '''
      }
    }
   
    stage('Deploy_To_TestServer') {
      steps {
        echo '***** DEPLOYING TO TEST SERVER *****'
        withCredentials([string(credentialsId: 'SERVERPASS', variable: 'SERVERPASS')])    {
          sh "sshpass -p ${SERVERPASS} ssh -o StrictHostKeyChecking=no ubuntu@54.198.218.245 'docker-compose down && docker-compose up -d'"
        }
        echo '***** DEPLOYMENT IS DONE FOR TEST SERVER *****'
      }
    }

    stage('Deploy_To_ProdServer') {
      input {
        message "Would you like to Start Deploying to the Production Server? "
        ok "Yes, Start Deployment to The Production!"
      }
      steps {
        echo '***** DEPLOYING TO PRODUCTION SERVER *****'
        withCredentials([string(credentialsId: 'SERVERPASS', variable: 'SERVERPASS')]) {
          sh "sshpass -p ${SERVERPASS} ssh -o StrictHostKeyChecking=no ubuntu@54.173.227.97 'docker-compose down && docker-compose up -d'"
        }
        echo '***** DEPLOYMENT IS COMPLETED FOR PRODUCTION SERVER *****'
      }
    }
  }
}
