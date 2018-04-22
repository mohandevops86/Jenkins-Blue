pipeline {
  agent any
  stages {
    stage('Define Git Repo') {
      steps {
        git(url: 'https://github.com/cit-latex/t1-student-maven-proj.git', branch: 'master')
      }
    }
    stage('Maven Compile') {
      steps {
        sh 'mvn compile'
      }
    }
    stage('Generate Inventory File') {
      steps {
        sh '''cd /var/lib/jenkins/ansible
              gcloud compute instances list --filter "labels.env:dev" | grep -v ^NAME |awk \'{print $1}\' >inventory
           '''
      }
    }
    stage('Checking ssh connection') {
      parallel {
        stage('Checking ssh connection') {
          steps {
            sh '''
          cd /var/lib/jenkins/ansible
          ansible -i inventory all -m ping
        '''
          }
        }
        stage('') {
          steps {
            catchError() {
              sh '''
              cd /var/lib/jenkins/ansible
              for server in `cat inventory` ; do 
                gcloud compute instances start $server
              done 
              sleep 10
              '''
            }
            
          }
        }
      }
    }
  }
}