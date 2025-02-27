pipeline {
    agent any

    environment {
        MAVEN_HOME = "/usr/share/maven"
        DOCKER_IMAGE = "shatakshee/scientific_calculator:latest"

    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ShataksheeMishra/Scienntific_calculator'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }

        }
        stage('Build Docker Image') {
                    steps {
                        sh "docker build -t $DOCKER_IMAGE ."
                    }
                }

                stage('Login to Docker Hub') {
                    steps {
                        withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                            sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        }
                    }
                }

                stage('Push Docker Image') {
                    steps {
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
         stage('Run Ansible Playbook') {
                    steps {
                        withCredentials([usernamePassword(credentialsId: 'local-jenkins', usernameVariable: 'ANSIBLE_USER', passwordVariable: 'ANSIBLE_PASS')]) {
                            sh '''
                            ansible-playbook -i inventory.ini deploy.yml --extra-vars "ansible_user=$ANSIBLE_USER ansible_ssh_pass=$ANSIBLE_PASS"
                            '''
                        }
                    }
                }

        stage('Deploy') {
            steps {
                echo 'Deployment step done'
            }
        }
    }

   post {
       success {
           echo 'Build and tests passed successfully!'
          body: "The build passed successfully!"
            mail to: 'shatakshee.mishra@iiitb.ac.in',
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",

       }
       failure {
           echo 'Build or tests failed!'
            mail to: 'shatakshee.mishra@iiitb.ac.in',
                    subject: "Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Oops! The build failed. Check the logs for details."

       }
   }
//trial2

}