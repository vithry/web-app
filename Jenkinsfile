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
        stage('Test') {
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
        }
        stage('Build Docker Img'){
          steps {
            script {
              sh "sudo chown -R $USER: ${WORKSPACE}"
              writeFile file: "Dockerfile", text: """FROM java:8-jdk-alpine
              COPY ./target/dependency/jetty-runner.jar /usr/app/
              WORKDIR /usr/app
              EXPOSE 8080
              ENTRYPOINT ["java", "-jar", "jetty-runner.jar"]
            """
              dockerImg = docker.build("example/web-app", '.')
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
