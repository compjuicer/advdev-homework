def GUID = "2043"

def mvnCmd = "mvn -s ./nexus_openshift_settings.xml"

def devProject  = "${GUID}-tasks-dev"
def prodProject = "${GUID}-tasks-prod"

def activeApp   = ""
def destApp     = "tasks-green"

def devTag      = "0.0-0"
def prodTag     = "0.0"

pipeline {

  agent {
    kubernetes {
      label "homework"
      cloud "openshift"
      inheritFrom "maven"
      containerTemplate {
        name "jnlp"
        image "image-registry.openshift-image-registry.svc:5000/${GUID}-jenkins/jenkins-agent-appdev:latest"
        //image "docker-registry.default.svc:5000/${GUID}-jenkins/jenkins-agent-appdev:latest"
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
        script {
          def pom = readMavenPom file: 'pom.xml'
          def version = pom.version

          devTag  = "${version}-" + currentBuild.number
          prodTag = "${version}"
        }
      }
    }

    stage('Build War File') {
      steps {
        echo "Building version ${devTag}"

        //sh "${mvnCmd} -DskipTests clean package"
        sh "${mvnCmd} -DskipTests package"
      }
    }

    stage('Unit Tests') {
      steps {
        echo "Running Unit Tests"

        sh "${mvnCmd} test"
      }
    }

    //Using Maven call SonarQube for Code Analysis
    stage('Code Analysis') {
      steps {
        echo "Running Code Analysis"

        //sh "mvn sonar:sonar -s ./nexus_settings.xml -Dsonar.host.url=http://sonarqube.gpte-hw-cicd.svc.cluster.local:9000"
        sh "mvn sonar:sonar -s ./nexus_settings.xml -Dsonar.host.url= http://sonarqube-gpte-hw-cicd.apps.na311.openshift.opentlc.com"
      }
    }

    // Publish the built war file to Nexus
    stage('Publish to Nexus') {
      steps {
        echo "Publish to Nexus"

        //sh "mvn -s ./nexus_settings.xml deploy -DskipTests=true -DaltDeploymentRepository=nexus::default::https://nexus-registry.gpte-hw-cicd.svc.cluster.local:5000/repository/releases/${GUID}"
        sh "mvn -s ./nexus_settings.xml deploy -DskipTests=true -DaltDeploymentRepository=nexus::default::http://nexus3-gpte-hw-cicd.apps.na311.openshift.opentlc.com/repository/releases/${GUID}"
      }
    }

  }
}
