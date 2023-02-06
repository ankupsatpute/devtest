pipeline {
    agent any
	environment {
        PATH = "/opt/apache-maven-3.8.7/bin/:$PATH" 
	}
     tools {
       terraform 'terraform'
       }   
  stages{
      stage('Git checkout'){
          steps{
            git branch: 'master', url: 'https://github.com/ankupsatpute/devtest.git'
          }
      }
    
        stage('Maven Build') {
            steps {
                sh "mvn package"
                echo 'Maven Build Completed'
            }
        }
       
       stage('Ansible & Terraform provisoining') {
            steps {
                script {
                      sh '''
                        cd Terraform

                        sed -i "s/server_name/${SERVER_NAME}/g" backend.tf
                        export TF_VAR_name=${SERVER_NAME}

                        terraform init
                        terraform plan
                        terraform $TERRAFORM_ACTION -auto-approve
                     '''
                }
            }
        }
	    stage('Ansible provisoining') {
      steps {
        retry(count: 5) {
         sh 'ansible-playbook  Ansible/tomcat.yaml -e ansible_python_interpreter=/usr/bin/python --check'
        }
      }
    }
    }
}
