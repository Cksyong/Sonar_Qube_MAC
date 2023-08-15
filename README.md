# macOS
1. 관련 프로그램 다운로드 
1-1) 환경 구축을 위한 폴더 생성
- Applications(응용프로그램)/StaticCodeAnalysis 폴더 생성
- Applications(응용프로그램)/StaticCodeAnalysis/Downloads 폴더 생성

1-2) 관련 프로그램 및 자료 다운로드

1-2-1) maven
https://maven.apache.org/download.cgi
- apache-maven-3.9.4-bin.zip

1-2-2) OpenJDK
https://adoptium.net/temurin/releases/
- 
-OpenJDK17U-jdk_x64_windows_hotspot_17.0.8_7.zip

1-2-3) gradle
https://gradle.org/releases/
-gradle-8.2-bin.zip

1-2-4) sonarqube
https://www.sonarsource.com/open-source-editions/
-sonarqube-10.1.0.73491.zip

1-2-5) sonarqube 리포트 플러그인
https://github.com/cnescatlab/sonar-cnes-report/releases
- sonar-cnes-report-4.2.0.jar

1-2-6) react프로젝트를 위한 nodejs 설치
https://nodejs.org/ko/download
-node-v18.17.1-win-x64.zip

1-2-7)  python
https://www.python.org/downloads/macos/
-python-3.10.10-embed-amd64.zip



참고자료
https://www.kisa.or.kr/2060204/form?postSeq=5&lang_type=KO&page=1
-소프트웨어_개발보안_가이드(2021.12.29).pdf 

https://www.kisa.or.kr/2060204/form?postSeq=10&lang_type=KO
-공개SW를_활용한_소프트웨어_개발보안_점검가이드.pdf


https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:dev:imp:inspection
https://www.egovframe.go.kr/wiki/lib/exe/fetch.php?media=egovframework:dev:imp:egovinspectionrules.zip
- 전자정부프레임워크 PMD Rule


2. 환경구축

2-1)maven, OpenJDK, sonarqube 압축해제
-apache-maven-3.9.4
  jdk-17.0.8+7
  gradle-8.2
  node-v18.17.1-win-x64
  python-3.10.10-embed-amd64
  sonarqube-10.1.0.73491

2-2) 환경변수 설정
Applications/StaticCodeAnalysis\startSonarQube.sh  파일 생성


SET JAVA_HOME=Applications/StaticCodeAnalysis/jdk-17.0.8+7
SET MAVEN_HOME=Applications/StaticCodeAnalysis/apache-maven-3.9.4
SET GRADLE_HOME=Applications/StaticCodeAnalysis/gradle-8.2
SET SONARQUBE_HOME=Applications//StaticCodeAnalysis/sonarqube-10.1.0.73491

SET NODEJS_HOME=Applications/StaticCodeAnalysis/node-v20.5.0-win-x64

export PATH="Applications/StaticCodeAnalysis/bin:/Applications/bin:/bin:$JAVA_HOME/bin:$MAVEN_HOME/bin:$GRADLE_HOME/bin:$SONARQUBE_HOME/bin:$NODEJS_HOME:$PYTHON_HOME"

start %SONARQUBE_HOME%/bin/macosx-universal-64/sonar.sh

3. sonarqube 실행 및 플러그인 설치
3-1) 실행
- cmd창 관리자로 실행
최단 루트경로로 가서 (Macintosh HD)
/Applications/StaticCodeAnalysis/Downloads/sonarqube-10.1.0.73491/bin/macosx-universal-64/sonar.sh console


3-2) 로그인
- http://localhost:9000
- admin/admin
- 패스워드 변경

- How do you want to create your project?

3-3) 플러그인 설치
- 메뉴: Administration -> Marketplace ->Plugins (I understand the risk) 클릭
=> PMD 검색( PMDEXTERNAL ANALYSERS) install(설치)
=> Findbugs 검색 (FindbugsEXTERNAL ANALYSERS) install(설치)
=> SonarQube needs to be restarted in order toinstall 2 plugins (Restart Server) 클릭


Applications/StaticCodeAnalysis\sonarqube-10.1.0.73491\extensions\downloads 에서 확인 가능
Applications/StaticCodeAnalysis\sonarqube-10.1.0.73491\extensions\plugins 폴더로 자동 이동.


엑셀 리포트 생성 플러그인
https://github.com/cnescatlab/sonar-cnes-report/releases에서 다운로드 받은 sonar-cnes-report-4.2.0.jar을
Applications/StaticCodeAnalysis\sonarqube-10.1.0.73491\extensions\downloads로 이동
SonarQube Restart Server하면 Applications/StaticCodeAnalysis\sonarqube-10.1.0.73491\extensions\plugins 폴더로 자동 이동함.

=> SonarQube needs to be restarted in order toinstall 2 plugins (Restart Server) 클릭

4. 소스코드 진단
4-1) 프로젝트 생성
메뉴: Project -> Manually 클릭

4-1-1) creat project
Project display name: StudentSoup
Project key : 자동생성됨
Main branch name: main

4-1-2) onboarding.create_project.new_code_definition.title
new_code_definition.question

new_code_definition.global_setting
new_code_definition.previous_version: 
new_code_definition.previous_version.description new_code_definition.previous_version.usecase


4-2) Project Settings ->Project Information
Locally 클릭 -> Provide a token ->Generate a project token 생성(1년)
Run analysis on your project -> Maven 선택
Execute the Scanner for Maven 옵션 복사
-----------------------------------------
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=StudentSoup \
  -Dsonar.projectName='StudentSoup' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_4704d37a122eea231b615d9b82e36e1234567890
---------------------------------------------------------

Execute the Scanner for Gradle
---------------------------
plugins {
  id "org.sonarqube" version "4.2.1.3168"
}

and run the following command:

./gradlew sonar \
  -Dsonar.projectKey=StudentSoup \
  -Dsonar.projectName='StudentSoup' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_4704d37a122eea231b615d9b82e36e1234567890
--------------------------------------------------------

and Other (for JS, TS, GO, Python, PHP, ...)


sonar-scanner \
  -Dsonar.projectKey=test_sonar \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_9f07111423a433dc7abf776ce8e2be021234567890

만약 java의 폴더만 진단하고 싶을 경우

sonar-scanner \
  -Dsonar.projectKey=test_sonar \
  -Dsonar.sources=src/main/java \
  -Dsonar.java.binaries=build/classes \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_f7a195b1d2559e163f1ae5e991fc741234567890

4-3) Quality Profiles 설정
메뉴: Quality Profiles -> Filter profiles by: java 검색

Sonar way가 DEFAULT인데, FindBugs + FB-ContribBU(768Rules) : Used에서 default로 선택.
   

4-4) 진단
- 자신의 프로젝트에 맞게 Maven 스크립트와 Gradle 스크립트 실행


gradlew sonar -Dsonar.projectKey=StudentSoup -Dsonar.projectName='StudentSoup' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_4704d37a122eea231b615d9b82e36e819fcd7d94


5. SW개발보안 가이드 적용
- 행정안전부 49개 보안약점 관련 공개SW 도구 룰(Rule) 적용



오류 
gradlew sonar -Dsonar.projectKey=StudentSoup -Dsonar.projectName='StudentSoup' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_4704d37a122eea231b615d9b82e36e819fcd7d94

실행시 Exception in thread "main" java.net.UnknownHostException: services.gradle.org 오류

1) 자신의 자바 버전 확인
 java -version

현재의 Terminal창에서
export JAVA_HOME=$(/usr/libexec/java_home -v 17) 




> Could not resolve all files for configuration ':querydsl'.
   > Could not download querydsl-apt-5.0.0.jar (com.querydsl:querydsl-apt:5.0.0)
      > Could not get resource 'https://repo.maven.apache.org/maven2/com/querydsl/querydsl-apt/5.0.0/querydsl-apt-5.0.0.jar'.
         > Could not GET 'https://repo.maven.apache.org/maven2/com/querydsl/querydsl-apt/5.0.0/querydsl-apt-5.0.0.jar'.
            > 알려진 호스트가 없습니다 (repo.maven.apache.org)
   > Could not download lombok-1.18.24.jar (org.projectlombok:lombok:1.18.24)
      > Could not get resource 'https://repo.maven.apache.org/maven2/org/projectlombok/lombok/1.18.24/lombok-1.18.24.jar'.

settings.gradle file:

pluginManagement {
    repositories {
        maven { url "https://plugins.gradle.org/m2/" }
        gradlePluginPortal()
    }
}





 "react-sweetalert2": "^0.5.2", -> "react-sweetalert2": "^0.6.0",

"node-sass": "^7.0.3", ->"node-sass": "^8.0.0",


자바 소스의 test폴더 제거
