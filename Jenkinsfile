pipeline {
    agent any
    environment{
        AZURE_CRED_ID="jenkins-appservice"
        RES_GROUP="rg-th01-sea-d-appservices-inter01"
        WEB_APP="aas-th01-sea-d-azecfp-icollect"
    }
    stages { 
        stage('SCM') {
            steps { 
              git 'https://github.com/smutchah/hello-azure.git'
            }
        }
        stage('build & SonarQube Scan') {
            steps { 
              withSonarQubeEnv('SonarQube') {
                sh 'mvn clean package sonar:sonar'
              } // SonarQube taskId is automatically attached to the pipeline context
            }
        }
        // No need to occupy a node
        stage("Quality Gate") {
            steps { 
              timeout(time: 15, unit: 'MINUTES') {
                //waitForQualityGate(abortPipeline: true)
              }
		  }
        }
        stage('Deploy') {
            steps {
              azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
                                 resourceGroup: env.RES_GROUP, 
                                 appName: env.WEB_APP, 
                                 filePath: '*.war',
                                 sourceDirectory: 'target',
                                 targetDirectory: 'webapps'
            }
        }
    }
}
