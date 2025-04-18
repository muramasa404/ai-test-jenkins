pipeline {
    agent any
    
    triggers {
        pollSCM('H/5 * * * *')  // 5분마다 GitHub 변경사항 확인
    }
    
    stages {
        stage('Collect Data') {
            steps {
                script {
                    // 현재 시간 가져오기
                    def currentHour = new Date().format('HH')
                    def currentMinute = new Date().format('mm')
                    
                    // API 엔드포인트 계산
                    def endpoint = (currentMinute.toInteger() + 1).toString()
                    if (endpoint.toInteger() > 60) {
                        endpoint = "1"
                    }
                    
                    // API 호출 및 JSON 저장
                    sh """
                        curl -s "https://jsonplaceholder.typicode.com/todos/${endpoint}" > test.json
                    """
                }
            }
        }
        
        stage('Commit and Push') {
            steps {
                script {
                    // Git 설정
                    sh """
                        git config --global user.email "jenkins@example.com"
                        git config --global user.name "Jenkins"
                    """
                    
                    // 변경사항 커밋 및 푸시
                    sh """
                        git add test.json
                        git commit -m "Update test.json with new data"
                        git push origin main
                    """
                }
            }
        }
    }
    
    post {
        always {
            // 빌드 후 정리 작업
            cleanWs()
        }
    }
} 