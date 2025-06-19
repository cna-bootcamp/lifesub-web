# 마이구독 서비스 (LifeSub)

## 1. 소개
마이구독은 다양한, 증가하는 생활 구독 서비스를 한 곳에서 편리하게 관리할 수 있는 애플리케이션입니다.   
사용자가 구독 중인 서비스를 한눈에 확인하고, 월별 구독료를 관리하며, 새로운 구독 서비스를 추천받을 수 있습니다.  

### 1.1 핵심 기능
- **구독 관리**: 다양한 구독 서비스를 한 곳에서 관리
- **비용 분석**: 월별 구독료 총액 및 수준 확인 (Liker, Collector, Addict)
- **맞춤형 추천**: 사용자의 지출 패턴 기반 새로운 구독 서비스 추천

### 1.2 MVP 산출물
- **발표자료**: {발표자료 링크}
- **설계결과**: {설계결과 링크}
- **Git Repo**: 
  - **프론트엔드**: https://github.com/cna-bootcamp/lifesub-web.git
  - **백엔드**: https://github.com/cna-bootcamp/lifesub.git
  - **manifest**: https://github.com/cna-bootcamp/lifesub-manifest.git
- **시연 동영상**: {시연 동영상 링크}  

## 2. 시스템 아키텍처

### 2.1 전체 구조
프론트엔드와 마이크로서비스 백엔드로 구성된 웹 애플리케이션  
{전체 서비스와 관계를 표현한 Context Map이나 논리아키텍처}

### 2.2 마이크로서비스 구성
- **회원 서비스 (Member)**: 사용자 인증 및 토큰 관리
- **구독 서비스 (MySub)**: 구독 정보 관리 및 카테고리 관리
- **추천 서비스 (Recommend)**: 사용자 맞춤형 구독 서비스 추천

### 2.3 기술 스택
- **프론트엔드**: React, Material UI, React Router
- **백엔드**: Spring Boot, Java
- **인프라**: Azure Kubernetes Service (AKS), Azure Container Registry
- **CI/CD**: Jenkins, Podman (컨테이너 빌드)
- **코드 품질**: SonarQube
- **백킹 서비스**:
  - **Database**: PostgreSQL
  - **Message Queue**: RabbitMQ
  - **기타**: Redis

## 3. 백킹 서비스 설치
1. Database 설치  
   ```bash
   # Helm 저장소 추가
   helm repo add bitnami https://charts.bitnami.com/bitnami
   helm repo update

   # Member 서비스용 DB
   helm install member bitnami/postgresql \
      --set global.postgresql.auth.postgresPassword=Passw0rd \
      --set global.postgresql.auth.username=admin \
      --set global.postgresql.auth.password=Passw0rd \
      --set global.postgresql.auth.database=member

   # MySub 서비스용 DB
   helm install mysub bitnami/postgresql \
      --set global.postgresql.auth.postgresPassword=Passw0rd \
      --set global.postgresql.auth.username=admin \
      --set global.postgresql.auth.password=Passw0rd \
      --set global.postgresql.auth.database=mysub

   # Recommend 서비스용 DB
   helm install recommend bitnami/postgresql \
      --set global.postgresql.auth.postgresPassword=Passw0rd \
      --set global.postgresql.auth.username=admin \
      --set global.postgresql.auth.password=Passw0rd \
      --set global.postgresql.auth.database=recommend
   ```

2. Message Queue 설치
  설치 방법  
  {MQ별 설치 방법 안내}



## 4. 빌드 및 배포

### 4.1 프론트엔드 빌드 및 배포
1. 컨테이너 이미지 빌드
   ```bash
   docker build \
     --build-arg PROJECT_FOLDER="." \
     --build-arg REACT_APP_MEMBER_URL="http://api.example.com/member" \
     --build-arg REACT_APP_MYSUB_URL="http://api.example.com/mysub" \
     --build-arg REACT_APP_RECOMMEND_URL="http://api.example.com/recommend" \
     --build-arg BUILD_FOLDER="deployment/container" \
     --build-arg EXPORT_PORT="18080" \
     -f deployment/container/Dockerfile-lifesub-web \
     -t {Image Registry주소}/lifesub/lifesub-web:latest .
   ```

2. 이미지 푸시
   ```bash
   docker push {Image Registry주소}/lifesub/lifesub-web:latest
   ```

3. Kubernetes 배포
   ```bash
   kubectl apply -f deployment/manifest/
   ```

### 4.2 백엔드 빌드 및 배포
1. 애플리케이션 빌드
   ```bash
   # 각 서비스 모듈을 개별적으로 빌드
   ./gradlew :member:clean :member:build -x test
   ./gradlew :mysub-infra:clean :mysub-infra:build -x test
   ./gradlew :recommend:clean :recommend:build -x test
   ```

2. 컨테이너 이미지 빌드 (각 서비스별로 수행)
   ```bash
   # Member 서비스
   docker build \
     --build-arg BUILD_LIB_DIR="member/build/libs" \
     --build-arg ARTIFACTORY_FILE="member.jar" \
     -f deployment/Dockerfile \
     -t {Image Registry주소}/lifesub/member:latest .

   # MySub 서비스
   docker build \
     --build-arg BUILD_LIB_DIR="mysub-infra/build/libs" \
     --build-arg ARTIFACTORY_FILE="mysub.jar" \
     -f deployment/Dockerfile \
     -t {Image Registry주소}/lifesub/mysub:latest .

   # Recommend 서비스
   docker build \
     --build-arg BUILD_LIB_DIR="recommend/build/libs" \
     --build-arg ARTIFACTORY_FILE="recommend.jar" \
     -f deployment/Dockerfile \
     -t {Image Registry주소}/lifesub/recommend:latest .
   ```

3. 이미지 푸시
   ```bash
   docker push {Image Registry주소}/lifesub/member:latest
   docker push {Image Registry주소}/lifesub/mysub:latest
   docker push {Image Registry주소}/lifesub/recommend:latest
   ```

4. Kubernetes 배포
   ```bash
   kubectl apply -f deployment/manifest/
   ```

### 4.3 테스트 
1) 프론트 페이지 주소 구하기   
```
kubens {namespace}
k get svc 
```

2) 로그인 
- ID: user01 ~ user05
- PW: P@ssw0rd$

## 5. 팀

- 오유진 "피오" - Product Owner
- 강동훈 "테키" - Tech Lead
- 김민지 "유엑스" - UX Designer
- 이준혁 "백개" - Backend Developer
- 박소연 "프개" - Frontend Developer
- 최진우 "큐에이" - QA Engineer
- 정해린 "데브옵스" - DevOps Engineer
