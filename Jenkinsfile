node('master'){
    def buildNumber = BUILD_NUMBER
    stage("Check out Code"){
        git branch: 'Testing', credentialsId: 'GitHubCredentials', 
        url: 'https://github.com/DeveopsWithAwsLearning/Ngnix.git'
    }
    stage("Build Docker image"){
       sh "docker build -t swamipeddineni/ngnix ." 
    }
    
    stage("Login into Dockerhub and Push Image"){
        withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
         sh "docker login -u swamipeddineni -p ${DockerHubPwd}"       
      }
     sh "docker push swamipeddineni/ngnix:latest"
    }
    stage("Deploy Appl"){
        sshagent(['Docker_Dep_SSH']) {
            sh 'scp -o StrictHostKeyChecking=no  docker-compose.yaml ubuntu@13.233.255.3:'
	    sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.255.3 docker rm -f ngnixcontainer || true"
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.233.255.3 docker-compose up -d'
  
  }
    }
	stage("Slack Notifications"){
	        slackSend channel: '#jenkinsnotifications',
	        color: 'Good', 
		failOnError: true, 
		message: 'Build is successfull', 
		teamDomain: 'amdocsnotifications', 
		tokenCredentialId: 'Slack_Id'
}


}
