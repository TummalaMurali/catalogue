pipeline {
    agent { node { label 'AGENT-1' } }
    //global variable declaration under environment
    environment{
        packageVersion = ''
    }
    stages {
        stage ('Get version') {
            steps{
                script{
                    def packageJson = readJSON(file: 'package.json')
                    packageVersion = packageJSON.version
                    echo "version: ${packageVersion}"
                }
            }
        }
        stage('Install depdencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Unit test') {
            steps {
                echo "unit testing is done here"
            }
        }
        // sonar-scanner command expect sonar-project.properties should be available
        // stage('Sonar Scan') {
        //     steps {
        //         sh 'ls -ltr'
        //         sh 'sonar-scanner'
        //     }
        // }
        stage('Sonar Scan') {
            steps {
                echo "Sonar scan done"
            }
        }    
        stage('Build') {
            steps {
                sh 'ls -ltr'
                sh 'zip -r catalogue.zip ./* --exclude=.git --exclude=.zip'
            }
        }
        //SAST - StaticApplicationSecurityTesting
        stage('SAST') {
            steps {
                echo "SAST Done"
            }
        }

        //make sure to install pipeline utility steps plugin on jenkin server
        stage('Publish Artifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '172.31.62.20:8081//',
                    groupId: 'com.roboshop',
                    version: "$packageVersion",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'catalogue',
                        classifier: '',
                        file: 'catalogue.zip',
                        type: 'zip']
                    ]
                )
            }
        }
        //here downstream job need to be configured
        //pass package version deployment
        //this job will wait until downstream job is over
        // stage('Deploy') {
        //     steps {
        //         echo "Deployment"
        //         def params = [
        //             string(name: 'version', value: "$packageVersion")
        //         ]
        //         build job: "../catalogue-deploy", wait: true, parameters: params
        //     }
        // }
    }

    post{
        always{
            echo 'cleaning up workspace'
            deleteDir()
        }
    }
}