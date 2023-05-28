#!/usr/bin/env groovy 

node {
    stage('checkout') {
        checkout scm
    }


    def Version
    stage ('create Image Version'){
    Version = sh (
    script: "date +'%Y-%m-%d-%H.%M'",
    returnStdout: true,
       )
    }

    def dockerImage
    stage('build docker') {
        sh "mkdir docker"
        sh "cp Dockerfile docker && cp index.html docker"
        dockerImage = docker.build('vrank18/demo', 'docker')
    }

    stage('publish docker') {
        docker.withRegistry('vrank18',) {
            dockerImage.push "${orrVersion}"
        }
    }

    #stage('delete docker Image') {
    #    sh "docker rmi crisil/orrmodule"
    #    sh "docker rmi 120.20.2.142:37719/crisil/orrmodule:${orrVersion}"
    #    }

    stage('run in 120.20.2.142') {
  		sshagent (credentials: ['root1020116']) {
        sh "echo 'orrVersion=${orrVersion}' > .env"
        sh "scp -o StrictHostKeyChecking=no .env hex-dev-team@120.20.2.142:/var/workspace/LOS/docker-compose"
  			sh "ssh -o StrictHostKeyChecking=no -l hex-dev-team 120.20.2.142 /var/workspace/LOS/docker-compose/helper/orrmodule.sh"
  		}

      stage('Clean workspace') {
          sh "echo 'Cleaning workspace....'"
          deleteDir() /* clean up our workspace */
          }

		step([$class: 'Mailer',
            notifyEveryUnstableBuild: true,
            recipients: "CRISRADDEV@hexaware.com",
            sendToIndividuals: true])
    }
}
