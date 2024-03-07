pipeline {

    agent any
    stages {

        stage('Checkout Codebase'){
            steps{
                cleanWs()
                checkout scm: [$class: 'GitSCM', branches: [[name: '*/main']],userRemoteConfigs:
                [[credentialsId: 'github-kutay-private', url: 'git@github.com:kutaybekleric/junit-automtaion.git']]]
            }
        }

        stage('Build'){
            steps{
                sh 'mkdir lib'
                sh 'cd lib/ ; wget https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.7.0/junit-platform-console-standalone-1.7.0-all.jar'
                sh 'cd src ; javac -cp "../lib/junit-platform-console-standalone-1.7.0-all.jar" CarTest.java Car.java App.java'
            }
        }

        stage('Test'){
            steps{
                sh 'cd src/ ; java -jar ../lib/junit-platform-console-standalone-1.7.0-all.jar -cp "." --select-class CarTest --reports-dir="reports"'
                junit 'src/reports/*-jupiter.xml'
            }
        }

        stage('Deploy'){
            steps{
                sh 'cd src/ ; java App' 
            }
        }
    }


    post {
        failure {
            sendStatusEmail('kutay.bekleric@aselsis.com.tr', 'FAILED'
            slackSend channel: 'D069EPAU53P', message: "Build of $env.GIT_BRANCH failed! Details: $env.BUILD_URL")
        }
        success {
            sendStatusEmail('kutay.bekleric@aselsis.com.tr', 'SUCCESSFUL')
            slackSend channel: 'D069EPAU53P', message: "Build of $env.GIT_BRANCH was successful! Details: $env.BUILD_URL"
        }
    }

}

def sendStatusEmail(emailAddress, status) {
    renderStatus = status == 'SUCCESSFUL' ? "<b style='color:#008000'>$status</b>" : "<b style='color:#FF0000'>$status</b>"
    mail from: '"Muhittin" <kutay.bekleric@aselsis.com.tr>',
        to: emailAddress,
        mimeType: 'text/html',
        charset: 'UTF-8',
        subject: "[muhittin] Build of $env.GIT_BRANCH was $status",
        body: """
            Build <b>#$env.BUILD_NUMBER</b> of <b>$env.GIT_BRANCH</b> for commit $env.GIT_COMMIT was $renderStatus on <b>$env.NODE_NAME</b>.</br>
            Details: $env.BUILD_URL
        """.stripIndent()
}

