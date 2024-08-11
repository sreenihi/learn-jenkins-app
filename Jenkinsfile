pipeline {
  agent any
  stages {
    stage('Build') {
      agent {
        docker {
          image 'node:18-alpine'
          reuseNode true
        }
      }
      post {
    always {
      sh 'ls -la jest-results'
      echo " File not found let we create "
      sh 'touch jest-results'
      junit 'jest-results/junit.xml'
    }
  }
      steps {
        sh '''
          ls -la
          node --version
          npm --version
          npm ci
          npm run build
          ls -la
        '''
      }
    }

    stage('Parallel Stages') {
      parallel {
        stage('Test') {
          agent {
            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          }
          steps {
            sh '''
              mkdir -p jest-results
              npm test
              ls -la jest-results
            '''
          }
        }

        stage('E2E') {
          agent {
            docker {
              image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
              reuseNode true
            }
          }
          steps {
            sh '''
              npm install serve
              node_modules/.bin/serve -s build &
              sleep 10
              npx playwright test
            '''
          }
        }
      }
    }
  }

  
}
