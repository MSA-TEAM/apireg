node {

    stage ('Checkout') {
        checkout([$class: 'GitSCM',
            branches: [[name: '*/master']],
            doGenerateSubmoduleConfigurations: false, extensions: [],
            submoduleCfg: [],
            userRemoteConfigs: [[credentialsId: 'DevPro', url: 'https://devpro.ktds.co.kr/msa/apireg.git']]])
    }

    stage ('Script'){
        sh 'ssh ec2-user@ip-172-31-13-153 "mkdir -p /home/ec2-user/registry/log"'
        sh 'scp ./start.sh ec2-user@ip-172-31-13-153:/home/ec2-user/registry'
        sh 'scp ./shutdown.sh ec2-user@ip-172-31-13-153:/home/ec2-user/registry'
        sh 'ssh ec2-user@ip-172-31-13-153 "chmod a+x /home/ec2-user/registry/*.sh"'
    }

    stage ('Shutdown Server'){
        sh 'ssh ec2-user@ip-172-31-13-153 "/home/ec2-user/registry/shutdown.sh || true"'
    }

    stage ('Build') {
        sh './gradlew clean build docker'
    }

    stage ('Distribution') {
        sh 'scp build/libs/registry-1.0.0-RELEASE.jar ec2-user@ip-172-31-13-153:/home/ec2-user/registry'
    }
}