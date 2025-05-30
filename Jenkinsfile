pipeline {
    agent any

    stages {
        /*
        stage('Build') {
            agent {
                docker  {
                    image 'node:18-alpine'
                    reuseNode true
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
        */

        stage('stage Test'){
            parallel{
                stage('Unit test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Test stage'
                sh '''
                    if [ -f build/index.html ]; then
                      echo "✅ index.html found."
                    else
                      echo "❌ index.html not found."
                      exit 1
                    fi

                    npm test
                '''
            }

                post {
        always {
            junit 'jest-results/junit.xml'
        }
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
                    npm install -g serve
                    serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }

                post {
        always {
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
        }
            }
        }
        
    }
}
