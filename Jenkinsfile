def dockerRegistryCredentials = 'dockerhub'

pipeline {
    agent any
    environment {
        PROJECT_ID = 'southern-shard-316802'
        CLUSTER_NAME = 'kubegkecluster'
        LOCATION = 'us-east1'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
           /* steps {
                script {
                   myapp = docker.build("vveereshvsh/node-app:${env.BUILD_ID}")
                  //  sh "sudo docker build -t vveereshvsh/node-app:${env.BUILD_ID} ."
                }
            } 
            
           /* steps {
                   dockerCreateRepository credentialsId: dockerRegistryCredentials,
                      repository: 'vveereshvsh/node-app'
                   dockerBuildAndPush(tags: ["${env.BUILD_ID}"],
                      credentialsId: dockerRegistryCredentials,
                      image: 'vveereshvsh/node-app',
                      projectModel: packageJSON)
                  } */
            steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        def customImage = docker.build("vveereshvsh/node-app:${env.BUILD_ID}")
                        customImage.push()
                    }
                }
            }
        } 
       /* stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }   */     
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}
