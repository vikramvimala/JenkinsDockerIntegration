node
{
    	def buildNumber = BUILD_NUMBER 
    
	stage('Git Clone')
	{
		git url: 'https://github.com/vikramvimala/JenkinsDockerIntegration.git',branch: 'master'
    	}
    
	stage(" Maven Clean Package")
    	{
		def mavenHome =  tool name: "Maven", type: "maven"
		def mavenCMD = "${mavenHome}/bin/mvn"
      		sh "${mavenCMD} clean package"    
  	} 
    
	stage('Build Docker Image')
    	{    
		sh "docker build -t vikramvimala/java-docker-app:${buildNumber} ."
    	}
    
	stage('Push Docker Image')
        {
		withCredentials([string(credentialsId: 'dockerPassword', variable: 'dockerPassword')]) 
		{
			sh "docker login -u vikramvimala -p ${dockerPassword}"
        	}
		sh "docker push vikramvimala/java-docker-app:${buildNumber}"
     	}
     
	stage('Run Docker Image In Deployment Server')
    	{
		def dockerRun = "docker run -d -p 8080:8080 --name java-docker-app vikramvimala/java-docker-app:${buildNumber}"
        	sshagent(['dockerDevServerSSH']) 
		{  
			sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.235.104.22 docker stop java-docker-app || true'
			sh 'ssh  ubuntu@13.235.104.22 docker rm java-docker-app || true'
			sh 'ssh  ubuntu@13.235.104.22 docker rmi -f  $(docker images -q) || true'   
			sh "ssh  ubuntu@13.235.104.22 ${dockerRun}"
		}
	} 
}

    
    
