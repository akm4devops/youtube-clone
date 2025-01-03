def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/akm4devops/youtube-clone.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install -g svgo"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build -t youtube-clone ."
                       sh "docker tag youtube-clone akm4devops/youtube-clone:latest "
                       sh "docker push akm4devops/youtube-clone:latest "
                       sh "docker "
                    }
                }
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name youtube-clone -p 3000:3000 akm4devops/youtube-clone:latest'
            }
        }
    }
    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#the-cloud-hub',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}