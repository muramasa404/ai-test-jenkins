pipeline {
    agent any
    
    environment {
        // 환경 변수 설정
        PATH = "/usr/local/bin:/usr/bin:/bin"
    }
    
    triggers {
        pollSCM('H/5 * * * *')
    }
    
    stages {
        stage('Setup') {
            steps {
                script {
                    try {
                        // 필요한 도구 설치 확인
                        sh """
                            which curl || echo "curl not found"
                            which git || echo "git not found"
                            curl --version
                            git --version
                        """
                    } catch (Exception e) {
                        echo "Error in Setup stage: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
        
        stage('Collect Data') {
            steps {
                script {
                    try {
                        // 현재 시간 가져오기
                        def currentHour = new Date().format('HH')
                        def currentMinute = new Date().format('mm')
                        
                        echo "Current time: ${currentHour}:${currentMinute}"
                        
                        // API 엔드포인트 계산
                        def endpoint = (currentMinute.toInteger() + 1).toString()
                        if (endpoint.toInteger() > 60) {
                            endpoint = "1"
                        }
                        
                        echo "Calculated endpoint: ${endpoint}"
                        
                        // API 호출 및 JSON 저장
                        sh """
                            set -x  # 명령어 실행 로깅 활성화
                            curl -v "https://jsonplaceholder.typicode.com/todos/${endpoint}" > test.json || exit 1
                            ls -l test.json
                            pwd
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
      
    }
} 
