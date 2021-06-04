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
        stage('HTML report'){
            echo "HTML Report generation in progress"
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'src/main/resources/static/', reportFiles: 'report.html', reportName: 'HTML Report of the Application', reportTitles: ''])
        }
        stage('Build Docker Image'){
            echo "Building docker image for addressbook application ..."
            sh "${dockerCMD} build -t 199378/bootcampapp:${tagName} ."
        }
        stage("Push Docker Image to Docker Registry"){
            echo "Pushing image to docker hub"
            withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh "${dockerCMD} login -u ${username} -p ${password}"
                sh "${dockerCMD} push 199378/bootcampapp:${tagName}"
            }
        }
        stage('Deploy Application Using Docker'){
            echo "Deploying application usind docker via ansible"
            ansiblePlaybook credentialsId: 'ansibleprivatekey', installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'casestudy-playbook.yml'
        }
    }
}
catch(Exception err){
    echo "Exception occured..."
    currentBuild.result="FAILURE"
    emailext body: 'Your build is aborted.', subject: 'Build Failure', to: 'revarawat13@gmail.com'
}
finally {
    echo "email for every build"
    emailext body: 'This is to inform the status of build', subject: 'Build Status', to: 'revarawat13@gmail.com'
}
