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
        stage('Deploy to Prod') {
            agent {label 'production_Server'}
            steps {
                script {
                    //def imageName = 'ommaxyl/job1:latest'
                    //def host = '172.31.94.100'
                    //def prodContainerName = 'productionContainer'
                    def remoteUser = 'ubuntu'
                    
                     //sh "ssh-keyscan -H ${host} >> ~/.ssh/known_hosts"
                     //sh "cat ssh-keyscan production"
                     
                    sshagent(['docker-hub-private-key']) {
                        sh 'sudo docker pull ommaxyl/job1:latest'
                        sh 'sudo docker stop productionContainer || true'
                        sh 'sudo docker rm productionContainer|| true'
                        sh 'sudo docker run -d --name productionContainer -p 80:80 ommaxyl/job1:latest'
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

   

    
