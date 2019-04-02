// node {
//   def server = Artifactory.server 'newtechacademy.jfrog.io'
//   def myGradleContainer = docker.image('gradle:jdk8-alpine')
//   myGradleContainer.pull()
//   stage('prep') {
//     checkout scm
//   }
//   stage('build') {
//      myGradleContainer.inside("-v ${env.HOME}/.gradle:/home/gradle/.gradle") {
//        sh 'cd complete && ./gradlew build'
//      }
//   }
//   stage('test') {
//      myGradleContainer.inside("-v ${env.HOME}/.gradle:/home/gradle/.gradle") {
//        sh 'cd complete && ./gradlew test'
//      }
//   }
//   stage('publish') {
//     def uploadSpec = """{
//       "files": [
//         {
//           "pattern": "complete/build/libs/gs-gradle-*.jar",
//           "target": "gradle-dev-local/academy/newtech/gs-gradle/1.0/gs-gradle-1.0-docker.jar"
//         }
//      ]
//     }"""
//     server.upload(uploadSpec)
//   }
// }

pipeline {

  agent none
  stages {
    stage('Build') {
      agent {
        docker {
          image 'maven:3-alpine'
          args '-v "$HOME"/.m2:/root/.m2'
          reuseNode true
        }
      }
      steps {
        sh 'mvn install -e -DskipTests=true'
      }
    }
    stage('Sample') {
      steps {
        echo 'This is stage 2'
      }
    }
    stage('Build Node') {
      agent {
        docker {
          image 'node:7-alpine'
        }
      }
      steps {
        sh 'node --version'
      }
    }
    stage('Build java') {
      agent {
        docker {
          image 'openjdk:8-jre'
        }
      }
      steps {
        sh 'java -version'
      }
    }
    stage ('upload artifacts to jfrog') {
      agent any
      steps {
        script {
          def server = Artifactory.newServer url: 'http://172.17.0.3:8081/artifactory/', username: 'admin', password: 'Admin123!'
          def uploadSpec = """{
              "files": [
                {
                  "pattern": "target/*.jar",
                  "target": "libs-release/MyTestApp/test.jar"
                }
              }
            ]
          }"""
          server.upload(uploadSpec)
        }
      }
    }
  }
}