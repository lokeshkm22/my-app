node{
    stage('SCM checkout')
    {
    git 'https://github.com/lokeshkm22/my-app.git'
    }
    stage('compile package'){
        def mvnHome = tool name: 'maven3', type:'maven'
        sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
        
    }
    stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    stage('Build Docker Imager'){
   sh 'docker build -t myweb:0.0.1 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u lokeshkm22 -p ${dockerPassword}"
    }
   sh 'docker push lokeshkm22/myweb:0.0.1'
}
stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 65.0.108.188:8083"
   sh "docker tag myweb:0.0.1 65.0.108.188:8083/lokesh:1.0.0"
   sh 'docker push 65.0.108.188:8083/lokesh:1.0.0'
   }
stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
}
stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest myweb:0.0.1' 
   }
}
