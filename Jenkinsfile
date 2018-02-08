node {
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

    echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"

    checkout scm


    stage('Test') {
        sh './gradlew check || true'
    }

    stage('Build') {
        try {
            sh './gradlew build'
            archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
        } catch(e) {
            mail subject: "Jenkins Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) failed with ${e.message}",
                to: 'jungim.kim@sicc.co.kr',
                body: "Please go to $env.BUILD_URL."
        }
    }

    stage('Archive') {
        archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
    }

    staget('Push') {
        sh './gradlew dockerPush'
    }

    stage('Deploy') {
        sh 'kubectl apply --namespace=development -f deployment.yaml'
    }
}