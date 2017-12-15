#!groovy
pipeline { 
  agent {
    docker {
      label 'docker'
      image 'bootswithdefer/docker-compose:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock -e USER=jenkins --group-add docker'
      reuseNode true
    }
  }

  environment {
    SANDBOX_REG = credentials("dtr_sandbox_jenkins")
  }
  
  options {
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  stages {
    stage('Build') {
      steps {
        script {
          sh "docker pull hello-world"
          sh "docker tag hello-world dtr.sandbox.asu.edu/jenkins/hello-world"
          sh "docker login --username=${env.SANDBOX_REG_USR} --password=${env.SANDBOX_REG_PSW} dtr.sandbox.asu.edu"
          sh "docker push dtr.sandbox.asu.edu/jenkins/hello-world"
        }
      }
    }
    stage('Deploy') {
      steps {
        script {
          withCredentials([dockerCert(variable: 'DOCKER_CERT_PATH',
                                      credentialsId: 'ucp_jenkins_sandbox')]) {
              sh """export DOCKER_HOST=tcp://ucp.sandbox.asu.edu:443
                    export DOCKER_TLS_VERIFY=1
                    docker version
                    export DOMAIN="hrm-http-example.sandbox.asu.edu"
                    docker stack deploy -c docker-compose.hrm.http.yml hrm-http-example"""
          }
        }
      }
    }      
  }
}
