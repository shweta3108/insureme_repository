def containerName="insure-me"
def tag="latest"
def dockerHubUser="shwetas27"
def httpPort="8083"

node {
    stage('git Checkout') {
        echo "checking code"
        checkout scm
    }
    stage('Build'){
        sh "mvn clean package"
    }
    stage('Publish Test Reports') {
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: false,
            reportDir: 'target/surefire-reports',
            reportFiles: 'index.html',
            reportName: 'HTML Report',
            reportTitles: '',
            useWrapperFileDirectly: true
        ])
    }
     stage("Image Prune"){
         sh "docker image prune -a -f"
    }
    stage('Image Build'){
        sh "docker build -t $containerName:$tag --no-cache ."
        echo "Image build complete"
    }
    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u $dockerUser -p $dockerPassword"
            sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
            sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
     }
    }
    stage('Run App') {
    ansiblePlaybook credentialsId: 'ansible', installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    } 
    stage('Selenium Test') {
        sleep(time: 60, unit: 'SECONDS') 
        sh 'sudo java -jar insure_me.jar'
           
    }     
   
}




