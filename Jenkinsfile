pipeline {
agent any
environment {
    IMAGE_NAME = "devopsawstrainer/devops-integration"
    IMAGE_TAG = "${BUILD_NUMBER}"
}
tools{
 jdk 'JAVA_HOME'
 maven 'M2_HOME'
 
}
 stages {
 stage("checkout"){
	   steps{
	       git branch: 'main', url: 'https://github.com/ashisnishanka/devops-docker-project1.git'
	   }
 }
  stage("maven build"){
	   steps{
	   sh 'mvn clean package'
	   }
 }
 stage('Docker Check') {
            steps {
                sh 'whoami'
                sh 'groups'
                sh 'docker ps'
            }
        }
  stage('Build docker image'){
            steps{
                script{
                      sh'docker ps'
                      sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }
stage('Trivy Scan') {
    steps {
        sh "trivy image ${IMAGE_NAME}:${IMAGE_TAG}"
    }
}
stage('Push image to Hub'){
            steps{
                script{
				     withCredentials([string(credentialsId: 'dockerhubpassword', variable: 'dockerpasswd')]) {
                   sh 'docker login -u devopsawstrainer -p ${dockerpasswd}'

}
                   sh 'docker push devopsawstrainer/devops-integration'
                }
            }
        }
stage('Deploy to EC2') {
    steps {
        sshagent(['deployserver']) {
            sh '''
            ssh -o StrictHostKeyChecking=no ec2-user@13.233.148.109 "
              docker pull devopsawstrainer/devops-integration &&
              docker stop myapp1 || true &&
              docker rm myapp1 || true &&
              docker run -d --name myapp1 -p 8080:8080 devopsawstrainer/devops-integration
            "
            '''
        }
    }
}
}
}
