node {
    def mvnHome = tool name: 'Maven', type: 'maven'
    def mvnCli = "${mvnHome}/bin/mvn"

    properties([
        disableConcurrentBuilds(), 
        [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/tguptaji/Jenkins-Example.git/'],
        parameters([string(defaultValue: 'DEV', description: 'env name', name: 'environment', trim: false)])
    ])
    stage('Checkout SCM'){
        git branch: 'master', credentialsId: 'github-creds', url: 'https://github.com/tguptaji/Jenkins-Example'
    }
    stage('Read praram'){
        echo "The environment chosen during the Job execution is ${params.environment}"
        echo "$JENKINS_URL"
    }
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven_3.6', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        sh "${mvnCli} clean compile"
    }
    stage('maven package'){
        sh "${mvnCli} package -Dmaven.test.skip=true"
    }
    stage('Archive atifacts'){
        archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
    }
    stage('Archive Test Results'){
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    stage('Deploy To Tomcat'){
        sshagent(['Node']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war root@172.31.29.254:/opt/apache-tomcat-7.0.94/webapps/'
        }
    }
    stage('Smoke Test'){
        sleep 5
        sh "curl 172.31.29.254:8080/petclinic"
    }

}
