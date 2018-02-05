properties([
    pipelineTriggers([
        [$class: 'GenericTrigger',
            genericVariables: [
                [expressionType: 'JSONPath', key: 'before', value: '$.before'],
                [expressionType: 'JSONPath', key: 'after', value: '$.after'],
                [expressionType: 'JSONPath', key: 'reference', value: '$.ref'],
                [expressionType: 'JSONPath', key: 'repository', value: '$.repository.full_name']
            ],
            genericRequestVariables: [],
            genericHeaderVariables: [],
            regexpFilterText: '$repository/$reference',
            regexpFilterExpression: 'MSA/apireg/refs/heads/master'
        ]
    ])
])

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