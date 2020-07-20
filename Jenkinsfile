def dockerImg
pipeline {
  agent any
  options {
    timestamps ()
  }

    stages {
        stage('Build') {
            agent {
              docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn -B -DskipTests clean package'

            }
        }
      /*  stage('Test') {
            agent {
              docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        } */
        stage('Build Docker Img'){
          steps {
            script {
              sh "sudo chown -R $USER: ${WORKSPACE}"
              writeFile file: "Dockerfile", text: """FROM tomcat:8.0-alpine
              COPY /var/lib/jenkins/workspace/web-app@2/target/hello-world-war-1.0.0.war /usr/local/tomcat/webapps/
              EXPOSE 8080
            """
              dockerImg = docker.build("web-app", '.')
            }

          }

        }

        stage('Push Docker Img') {
          steps{
            script{
              docker.withRegistry('http://localhost:8082') {
                  dockerImg.push("1.0")
              }
            }
          }

        }

      }
    }
