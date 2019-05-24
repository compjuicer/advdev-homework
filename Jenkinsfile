pipeline {
  agent {
    kubernetes {
      label "homework"
      cloud "openshift"
      inheritFrom "maven"
      containerTemplate {
        name "jnlp"
        image "image-registry.openshift-image-registry.svc:5000/2043-jenkins/jenkins-agent-appdev:latest"
        //image "docker-registry.default.svc:5000/2043-jenkins/jenkins-agent-appdev:latest"
        resourceRequestMemory "1Gi"
        resourceLimitMemory "2Gi"
        resourceRequestCpu "1"
        resourceLimitCpu "2"
      }
    }
  }
  stages {
    stage('Checkout Source') {
      steps {
       checkout scm
      }
    }
  }
}
