pipeline {
    agent any
    environment {
        DOCKER = credentials('docker-repository-credentials')
        OPENSHIFTREF = credentials('openshift4-internal-repo')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Image') {
            steps {
                sh "sudo docker build -f Dockerfile -t sysdigcicd/cronagent ."
            }
        }
        stage('Push Image (Staging repo)') {
            steps {
                sh "sudo docker login --username ${DOCKER_USR} --password ${DOCKER_PSW}"
                sh "sudo docker push sysdigcicd/cronagent"
                sh "echo docker.io/sysdigcicd/cronagent > sysdig_secure_images"
            }
        }
        stage('Scanning Image') {
            steps {
                sysdigSecure 'sysdig_secure_images'
            }
        }
        stage('Push Image (OpenShift internal repo)') {
            steps {
                sh "sudo docker login https://registry.apps.openshift4.openshift-sysdig.net --username ${OPENSHIFTREF_USR} --password ${OPENSHIFTREF_PSW}"
                sh "docker tag sysdigcicd/cronagent registry.apps.openshift4.openshift-sysdig.net/sysdig-image/cronagent:0.1"
                sh "docker push registry.apps.openshift4.openshift-sysdig.net/sysdig-image/cronagent:0.1"
            }
        }
   }
}
