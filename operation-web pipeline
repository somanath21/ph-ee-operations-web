pipeline { 

  environment { 
      registry = "oaftech.azurecr.io/phee-ns/ops-web" 
      registryCredential = 'azdockerpass' 
      registryIBM = "us.icr.io/phee-ns/ops-web" 
      registryCredentialIBM = 'ibmcr' 
      // args = "--progress=plain --no-cache ."
      // registrySawtel = "fynarfin.azurecr.io/ph-ee-ops-web" 
      // registrySawtelCredential = 'sawteldockerpass'
  }
  
  options {
        skipStagesAfterUnstable()
  }

  agent any 

  stages { 
      
      stage('Cleaning up') { 
          steps { 
              sh "docker image rmi -f \$(docker image ls -q) 2>&1 || true"
          }
      } 
      
      stage('Cloning from Git') { 
          steps { 
              git url: 'https://github.com/openmf/ph-ee-operations-web.git', branch: 'master' 
          }
      } 

      stage('Building OAF Azure image') { 
          steps { 
              script { 
                  dockerImage = docker.build registry + ":latest"
              }
          } 
      }

      stage('Deploy OAF Azure image') { 
          steps { 
              script { 
                  docker.withRegistry( 'https://oaftech.azurecr.io', registryCredential ) { 
                      dockerImage.push() 
                  }
              } 
          }
      } 
      
      stage('Restarting OAF Pods') {
          steps {
              script {
                  sh "kubectl config use-context oaf-uat"
                  sh "kubectl delete pods -l app=ph-ee-operations-web"
              }
          }
      }
      
      
      stage('Building IBM QA image') { 
          steps { 
              script { 
                  dockerImage = docker.build registryIBM + ":latest" 
              }
          } 
      }
      
      stage('Deploy IBM QA image') { 
          steps { 
              script { 
                  docker.withRegistry( 'https://us.icr.io', registryCredentialIBM ) { 
                      dockerImage.push() 
                  }
              }  
          }
      }

      
    }
}
