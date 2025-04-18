pipeline {
    agent any
    
    triggers {
        pollSCM('H/5 * * * *')
    }
    
    stages {
        stage('Collect Data') {
            steps {
                script {
                    try {
                        def currentHour = new Date().format('HH')
                        def currentMinute = new Date().format('mm')
                        
                        echo "Current time: ${currentHour}:${currentMinute}"
                        
                        def endpoint = (currentMinute.toInteger() + 1).toString()
                        if (endpoint.toInteger() > 60) {
                            endpoint = "1"
                        }
                        
                        echo "Calculated endpoint: ${endpoint}"
                        
                        sh """
                            curl -v "https://jsonplaceholder.typicode.com/todos/${endpoint}" > test.json
                            cat test.json
                        """
                    } catch (Exception e) {
                        echo "Error in Collect Data stage: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
        
        stage('Commit and Push') {
            steps {
                script {
                    try {
                        sh """
                            git config --global user.email "snoopyjch@gmail.com"
                            git config --global user.name "admin"
                            git status
                            git add test.json
                            git commit -m "Update test.json with new data"
                            git push origin main
                        """
                    } catch (Exception e) {
                        echo "Error in Commit and Push stage: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        failure {
            echo "Pipeline failed. Check the logs for more details."
        }
    }
} 