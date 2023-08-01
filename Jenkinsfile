pipeline {
    agent any

     environment{
       registryCredential = 'ecr:eu-west-1:cred'
       appRegistry = "175223620529.dkr.ecr.eu-west-2.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://175223620529.dkr.ecr.eu-west-2.amazonaws.com"
       cluster = "CapstoneProject"
        service = "capstoneproject-test"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/dhramns/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'cred', region: 'eu-west-2'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}