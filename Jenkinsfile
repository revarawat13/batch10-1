try{
    node{
        def mavenHome
        def mavenCMD
        def docker
        def dockerCMD
        def tagName = "1.0"
        stage('Preparation'){
            echo "Preparing the Jenkins environment with required tools..."
            mavenHome = tool name: 'maven 3', type: 'maven'
            mavenCMD = "${mavenHome}/bin/mvn"
            docker = tool name: 'docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
            dockerCMD = "$docker/bin/docker"
        }
        stage('code checkout'){
            echo "code checkout in progress"
            git 'https://github.com/revarawat13/batch10-1.git'
        }
        stage('Build, Test and Package'){
            echo "build in progress"
            sh "${mavenCMD} clean package"
        }
        stage('Sonar Scan'){
            echo "Code quality scanning in progress "
            sh "${mavenCMD} sonar:sonar -Dsonar.host.url=http://35.224.61.74:9000/"
        }
        stage('Build Docker Image'){
            echo "Building docker image for addressbook application ..."
            sh "${dockerCMD} build -t 199378/bootcampapp:${tagName} ."
        }
        stage("Push Docker Image to Docker Registry"){
            echo "Pushing image to docker hub"
            withCredentials([string(credentialsId: 'dockerPwd', variable: 'dockerHubPwd')]) {
                sh "${dockerCMD} login -u 199378 -p ${dockerHubPwd}"
                sh "${dockerCMD} push 199378/bootcampapp:${tagName}"
            }
        }
    }
}
catch(Exception err){
    echo "Exception occured..."
    currentBuild.result="FAILURE"
    //send an failure email notification to the user.
}
