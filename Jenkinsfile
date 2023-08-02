pipeline {
    agent {label 'testing_Server'}
   stages{
      stage("CodeClone"){
        steps{
          git credentialsId: 'github-Cred', url: 'https://github.com/ommaxyl/website.git'
        }
      }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = "ommaxyl/job1:latest"
                    def dockerfile = "Dockerfile"

                    withCredentials([string(credentialsId: 'docker-hub-credentials', variable: 'docker-hub-credentials')]) {
                        sh "sudo docker build -t ${imageName} -f ${dockerfile} ."
                        sh "sudo docker push ${imageName}"
                    }
                }
            }
        }
    }
}

pipeline {
    agent {label 'testing_Server'}
    stages {
        stage('Run Tests') {
            steps {
                script {
                    def imageName = "ommaxyl/job1:latest"

                    sh "sudo docker pull ${imageName}"
                    // Run your tests here, assuming you have the appropriate test commands
                    // For example: sh "docker run --rm ${imageName} npm test"
                }
            }
        }
    }
}
    
pipeline {
    agent {label 'production_Server'}
    stages {
        stage('Deploy to Prod') {
            steps {
                script {
                    def imageName = 'ommaxyl/job1:latest'
                    def host = 'production'
                    def prodContainerName = 'productionContainer'
                    def remoteUser = 'ubuntu'
                    
                     sh "ssh-keyscan -H ${host} >> ~/.ssh/known_hosts"
                     
                    sshagent(['docker-hub-private-key']) {
                        sh "ssh ${remoteUser}@${host} 'sudo docker pull ${imageName}'"
                        sh "ssh ${remoteUser}@${host} 'sudo docker stop ${prodContainerName} || true'"
                        sh "ssh ${remoteUser}@${host} 'sudo docker rm ${prodContainerName} || true'"
                        sh "ssh ${remoteUser}@${host} 'sudo docker run -d --name ${prodContainerName} -p 80:80 ${imageName}'"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment to production successful!"
        }
        failure {
            echo "Deployment to production failed!"
        }
    }
}