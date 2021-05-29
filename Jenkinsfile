try{
    node{
        def mavenHome
        def mavenCMD
        def docker
        def dockerCMD
        def scannerHome
        def tagName = "1.0"
        
        stage('Preparation')
        {
            echo "Preparing Jenkins environemt"
            mavenHome = tool name: "maven3.8", type: 'maven'
            mavenCMD = "${mavenHome}/bin/mvn"
            docker = tool name: 'docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
            dockerCMD = "$docker/bin/docker"
            scannerHome = tool name: 'sonar'
        }
        
        stage('git checkout')
        {
           echo "Checking out the code from git repository..."
           git 'https://github.com/deepanshu97971/batch10.git'
        }
        
        stage('Build, Test and Package')
        {
            echo 'Build, Test and Package'
            sh "${mavenCMD} clean package"
        }
        
        stage('Sonar Scan')
        {
            echo "Scanning application for vulnerabilities..."
            withSonarQubeEnv(credentialsId: 'sonar-key', installationName:'sonar')
            {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Test -Dsonar.projectName=Test -Dsonar.projectVersion=1.0 -Dsonar.sources=/var/lib/jenkins/workspace/$JOB_NAME/src -Dsonar.host.url=http://34.136.243.113:9000 -Dsonar.language=java -Dsonar.java.binaries=target"
            }
        }
        
        stage('Build Docker Image')
        {
            echo "Building docker image for application"
            sh "${dockerCMD} build -t deepanshu97971/case1:${tagName} ."
        }
        
        stage("Push Docker Image to Docker Registry")
        {
            echo "Pushing image to docker hub"
            withCredentials([usernamePassword(credentialsId: 'dockerHubPassword', passwordVariable: 'dockerPasswd', usernameVariable: 'dockerUser')])
            {
                sh "${dockerCMD} login -u ${dockerUser} -p ${dockerPasswd}"
                sh "${dockerCMD} push deepanshu97971/case1:${tagName}"
            }
            
        }
    }
}
catch(Exception e){
    
}
finally{
    
}
