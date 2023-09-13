pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME = "emman777"
        APP_NAME = "ui"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
        stages{
            stage('Checkout SCM'){
                steps{
                    script{
                        git branch: 'main', url: 'https://github.com/emmanuel5507/micro-ui.git'

                    }

                }
                     
            }
            stage('Build Docker image'){
              steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
              }  
            }
            stage('PUSH DOCKER IMAGE'){
              steps{
                script{
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
              }  
            }
            stage('Remove old DOCKER IMAGE'){
              steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                    }
                }
              }
              stage('updating k8s kube file'){
                steps{
                    script{
                        sh """
                        cat kube.yaml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' kube.yaml
                        cat kube.yaml
                        """
                    }
                }
              } 
               stage('update k8s kube file to git'){
                steps{
                    script{
                        sh """
                          git config --global user.name "emmanuel5507"
                          git config --global user.email "emmanelm73@gmail.com"
                          git add kube.yaml
                          git commit -m "updated the kube file"
                        """
                        withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                            sh "git push https://github.com/emmanuel5507/micro-ui.git main"
                        }
                       
                    }
                }
              }                
            }
            
 }


    
