---
layout: post
title: "Vue + Spring boot + Junit + Sonarqube 젠킨스 파이프라인 script 문법 예제 코드"
tags: Jenkins
comments: true
---

Jenkins Freestyle은 자료도 꽤 있고 groovy 코드가 아니고 UI로 하기 때문에 그렇게 어렵지 않다.
pipeline은 전부 코드를 직접 작성해야 하며 자료도 없을 뿐더러 Script, declarative 문법으로 나뉘어서
파편화가 심해 알맞는 자료를 찾기가 힘들었다.

그래서 pipeline 예제 코드를 조금 가공해서 적어두려고한다.
복사 붙여넣기로 사용할 수는 없어도 보고 참고는 가능하게 설명과 함께 적어두려고 한다.
세세한 설명에 대한 자료는 다른 곳에도 많아서 그냥 전체 코드를 써놓았고 어느 정도는 알고있다고 가정했다.

여기는 vue + spring boot + gradle test + Sonarqube 환경이다.

JDK 11 설치하고 jenkins 설정에서 global tool configuration에서 openjdk-11 적고 경로 설정 후

[https://support.cloudbees.com/hc/en-us/articles/204421664-Select-which-Java-JDK-to-use-in-Pipeline](https://support.cloudbees.com/hc/en-us/articles/204421664-Select-which-Java-JDK-to-use-in-Pipeline)

설정할때는 힘들었지만 한 번 해두니 확실히 편해져서 왜 사용하는지 알게됐다.

```groovy
node {
    // 서버의 JAVA_HOME을 바꿀 이유는 없다. stage 시작 전에 설정해주면 된다.
    env.JAVA_HOME="${tool 'openjdk-11'}"
    env.PATH="${env.JAVA_HOME}/bin:${env.PATH}"
    sh 'java -version'
    // 먼저 git에서 작업 할 프로젝트를 받아온다.
    stage('Ready') {
        script {
            try {
                git branch: 'feature/jenkins', credentialsId: 'za1a578c-2f11-7kjh-7dre-7e6789o5b098', url: 'git@123.456.0.99:testName/demo.git'
            } catch (Exception e) {
                throw slackSend (color: '#FF0033', message: "git Failure: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
    }
    // vue + spring boot의 경우 package-lock.json을 기준으로 빌드를 배포 전에 해야하기 때문에 여기서 작업한다.
    dir('vue') {
        stage('Build NPM') {
            script {
                try {
                    sh "npm ci; npm run build; npm audit fix"
                } catch (Exception e) {
                    throw slackSend (color: '#FF0033', message: "npm Failure: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                }
            }
        }
    }
    // 프론트 작업이 끝났으니 이제 Junit을 돌려준다. 그 전에 chmod로 실행권한을 부여해준다.
    stage('Gradle Junit Test') {
        script {
            try {
                sh "chmod +x gradlew; ./gradlew test"
            } catch (Exception e) {
                throw slackSend (color: '#FF0033', message: "Junit Test Failure: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
    }
    // 소나큐브도 돌려서 코드 퀄리티 검사를 항상 실시한다. 예제에는 비밀번호를 안적어도 된다고 해놨지만 실제로 해보니 -Dsonar.password도 적어줘야 한다.
    stage('Sonarqube') {
        withSonarQubeEnv('sonarqube_local') {
            sh './gradlew sonarqube -Dsonar.projectKey=vue-spring -Dsonar.host.url=http://123.456.0.789:9000 -Dsonar.login=admin -Dsonar.password=admin'
        }
    }
    // 이제 모든 검사도 끝났으니 bootJar 명령어로 Jar를 생성한다. 이제 여기안에 vue + spring boot가 전부 들어가게 된다.
    stage('Build Gradle') {
        script {
            try {
                sh "./gradlew bootJar"
            } catch (Exception e) {
                throw slackSend (color: '#FF0033', message: "Gradle Failure: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
    }
    // Junit 테스트 결과를 젠킨스 프로젝트 첫 화면에서 볼 수 있게 결과물을 출력한다. 이렇게 해주면 알아서 결과물을 보여준다.
    stage('Publish test results') {
        junit '**/build/test-results/test/*.xml'
    } 
    // 이제 작업한 서버에서 배포 서버로 보낸다. 보내고 실행까지 해야 한다면 명령어를 수정해야 한다.
    dir('build/libs') {
        stage('Deploy') {
            script {
                try {
                    sshagent (['za1a578c-2f11-7kjh-7dre-7e6789o5b098']) {
                        sh 'scp -o StrictHostKeyChecking=no demo.jar root@123.454.0.678:/usr/local/server'
                    }
                    slackSend (color: '#00FF00', message: "Deploy SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                } catch (Exception e) {
                    throw slackSend (color: '#FF0033', message: "Deploy Failure: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                }
            }
        }
    }
    /* clean up our workspace */
    deleteDir()
    /* clean up tmp directory */
    dir("${workspace}@tmp") {
        deleteDir()
    }
    /* clean up script directory */
    dir("${workspace}@script") {
        deleteDir()
    }
}
```