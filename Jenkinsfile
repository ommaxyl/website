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
                    sh 'npm install --global mocha chai puppeteer'
                    sh 'mocha test'
                    // Run your tests here, assuming you have the appropriate test commands
                    // For example: sh "docker run --rm ${imageName} npm test"
                }
            }
        }
    }
}
