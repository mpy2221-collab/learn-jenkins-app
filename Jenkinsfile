// - **1단계**
//     - Test 라는 이름의 새 스테이지를 생성한다.
//     - 해당 스테이지는 Build 스테이지 이후에 실행되어야 함.
//     - Test stage 라는 메시지를 출력한다.
//     - 변경 사항을 커밋/푸시한 후 업데이트된 파이프라인 빌드를 실행한다.
//     - 오류가 발생하지 않고 실행 로그에 메시지가 표시되는지 확인한다.

// - **2단계**
//     - 리눅스 명령어를 사용하여 해당 프로젝트의 테스트를 진행한다.
//     - build 폴더 안에 index.html 파일이 있는지를 테스트 하는 명령어를 작성해본다.
//     - Node 프로젝트를 테스트 하는 명령어를 작성해본다.
//     - 변경 사항을 커밋 푸시 한 후 업데이트된 파이프라인 빌드를 실행한다.
//     - 오류가 발생하지 않고 실행 로그에 메시지가 표시되는지 확인한다.

pipeline {
    agent {
        docker {
		        // 강의 자료와 버전을 맞춰주세요! 영상과는 다름!
            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
            reuseNode true
        }
    }

    environment{
        NETLIFY_SITE_ID = '6047bdf4-5d73-4b6b-a1e2-7f93172f5e87'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            

            steps {
                sh '''
                    echo "트리거 테스트 중....."
                    ls -al
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al

                '''
            }
        }

        stage('Test'){
            

            steps{
                sh '''
                    echo 'Test stage'

                    if test -f "build/index.html"; then
                        echo "index.html file is exist!!!"
                    else
                        echo "index.html file is not exist!!!"
                        exit 1
                    fi

                    npm test
                '''
            }
        }

        stage('E2E'){
            steps{
                sh'''
                    npm install serve
                    node_modules/.bin/serve -s build & sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }

        stage('Deploy'){
            steps{
                sh'''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "프로젝트 배포중... 사이트 아이디 : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }

    post{
        always{
            junit 'jest-results/junit.xml'
        }
    }
}