pipeline {
   agent none
   stages {
       stage("Build") {
           agent {
               docker {
                   image 'maven:3.6.3-jdk-8'
               }
           }
           steps {
               sh "mvn clean package -B -ntp -DskipTests -Dcheckstyle.skip"
           }
       }
       stage("Approval") {
           steps {
               script {

                   def users = "carlos"


                   timeout(time: 5, unit: 'MINUTES') {
                       userInput = input(
                           submitterParameter: 'approval',
                           submitter: "${users}",
                           message: "¿Desplegar en JBoss?", parameters: [
                           [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Aprobar']
                       ])
                   }


               }
           }
       }
       stage("Deploy on JBoss 7.2") {
           agent any
           steps {
               script {

                   echo "${userInput}"

                    if(userInput.Aprobar == true) {

                        sshagent ( credentials: ['jenkins-ssh-privatekey']){

                            sh """
                                scp -o StrictHostKeyChecking=no target/spring-petclinic-2.4.5.jar centos@54.149.65.202:/home/centos

                                ssh centos@54.149.65.202 '~/EAP-7.4.0/bin/jboss-cli.sh -c --command="undeploy spring-petclinic-2.4.5.jar"'
                                ssh centos@54.149.65.202 '~/EAP-7.4.0/bin/jboss-cli.sh -c --command="deploy /home/centos/spring-petclinic-2.4.5.jar"'

                            """
                        }
                    }

               }
           }
       }
   }
}
