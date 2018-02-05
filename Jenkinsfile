node {
    stage('Checkout') {
        checkout scm
    }

    stage('Test') {
        sh './gradlew check || true'
    }

    stage('Build') {
        try {
            sh './gradlew build dockerPush'
            archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
        } catch(e) {
            mail subject: "Jenkins Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) failed with ${e.message}",
                to: 'blue.park@kt.com',
                body: "Please go to $env.BUILD_URL."
        }
    }

    stage('Deploy') {
        sh 'kubectl apply -f deployment.yaml'
    }
}