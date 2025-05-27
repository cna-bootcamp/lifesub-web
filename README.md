# 마이구독 서비스 (LifeSub)


## 1. 소개
마이구독은 다양한, 증가하는 생활 구독 서비스를 한 곳에서 편리하게 관리할 수 있는 애플리케이션입니다. 사용자가 구독 중인 서비스를 한눈에 확인하고, 월별 구독료를 관리하며, 새로운 구독 서비스를 추천받을 수 있습니다.

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

## 3. 프론트엔드 (lifesub-web)

### 3.1 주요 화면
- **로그인 화면**: 사용자 인증
- **메인 대시보드**: 월별 구독료 및 구독 서비스 목록, 추천 카테고리 표시
- **구독 서비스 목록**: 카테고리별 구독 서비스 브라우징
- **구독 상세**: 구독 서비스 상세 정보 및 구독/취소 기능

### 3.2 API 연동
애플리케이션은 다음 백엔드 서비스와 API를 통해 통신합니다:
- `MEMBER_URL`: 인증 관련 API
- `MYSUB_URL`: 구독 관리 관련 API
- `RECOMMEND_URL`: 추천 관련 API

## 4. 백엔드 서비스

### 4.1 인증 서비스 (Member)
회원 로그인 및 로그아웃, JWT 토큰 관리를 담당합니다.

#### 4.1.1 주요 API
- `POST /login`: 로그인
- `POST /logout`: 로그아웃

### 4.2 구독 서비스 (MySub)
사용자의 구독 정보 관리 및 카테고리 관리를 담당합니다.

#### 4.2.1 주요 API
- `GET /total-fee`: 총 구독료 조회
- `GET /list`: 사용자 구독 목록 조회
- `GET /services/{id}`: 구독 서비스 상세 조회
- `POST /services/{id}/subscribe`: 구독 신청
- `DELETE /services/{id}`: 구독 취소
- `GET /categories`: 카테고리 목록 조회
- `GET /services`: 카테고리별 서비스 조회

### 4.3 추천 서비스 (Recommend)
사용자의 지출 패턴 기반 구독 서비스를 추천합니다.

#### 4.3.1 주요 API
- `GET /categories`: 추천 카테고리 조회

## 5. 개발 환경 설정

### 5.1 필수 요구사항
- Java 21 (백엔드)
- Node.js 20.x (프론트엔드)
- Docker 또는 Podman
- kubectl (Kubernetes CLI)
- Gradle (백엔드 빌드)

### 5.2 프론트엔드 개발 환경 설정
1. 저장소 클론
   ```bash
   git clone https://github.com/your-org/lifesub-web.git
   cd lifesub-web
   ```

2. 의존성 설치
   ```bash
   npm install
   ```

3. 개발 서버 실행
   ```bash
   npm start
   ```

4. 환경 설정
   프로젝트에서는 `public/runtime-env.js` 파일을 통해 런타임 환경 설정을 관리합니다:
   ```javascript
   window.__runtime_config__ = { 
       MEMBER_URL: 'http://localhost:8081/api/auth', 
       MYSUB_URL: 'http://localhost:8082/api/mysub', 
       RECOMMEND_URL: 'http://localhost:8083/api/recommend' 
   }
   ```

### 5.3 필요한 백킹 서비스 설치

#### 5.3.1 PostgreSQL 데이터베이스 설치
각 마이크로서비스는 별도의 PostgreSQL 데이터베이스를 사용합니다.

1. Docker로 설치
   ```bash
   # Member 서비스용 DB
   docker run -d --name member-postgres \
     -e POSTGRES_DB=member \
     -e POSTGRES_USER=admin \
     -e POSTGRES_PASSWORD=Passw0rd \
     -p 5432:5432 \
     postgres:13.2-alpine

   # MySub 서비스용 DB
   docker run -d --name mysub-postgres \
     -e POSTGRES_DB=mysub \
     -e POSTGRES_USER=admin \
     -e POSTGRES_PASSWORD=Passw0rd \
     -p 5433:5432 \
     postgres:13.2-alpine

   # Recommend 서비스용 DB
   docker run -d --name recommend-postgres \
     -e POSTGRES_DB=recommend \
     -e POSTGRES_USER=admin \
     -e POSTGRES_PASSWORD=Passw0rd \
     -p 5434:5432 \
     postgres:13.2-alpine
   ```

2. Kubernetes Helm Chart로 설치
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

#### 5.3.2 Message Queue 설치

### 5.4 백엔드 개발 환경 설정
1. 저장소 클론
   ```bash
   git clone https://github.com/your-org/lifesub.git
   cd lifesub
   ```

2. 의존성 설치 및 각 서비스 개별 빌드
   ```bash
   # 각 서비스 모듈을 개별적으로 빌드
   ./gradlew :member:build
   ./gradlew :mysub-infra:build
   ./gradlew :recommend:build
   
   # 또는 테스트 스킵 옵션으로 빌드
   ./gradlew :member:build -x test
   ./gradlew :mysub-infra:build -x test
   ./gradlew :recommend:build -x test
   ```

3. 각 서비스 실행
   ```bash
   # Member 서비스 실행
   java -jar member/build/libs/member.jar

   # MySub 서비스 실행 (mysub-biz는 라이브러리 모듈로 mysub-infra에 포함됨)
   java -jar mysub-infra/build/libs/mysub.jar

   # Recommend 서비스 실행
   java -jar recommend/build/libs/recommend.jar
   ```

4. 환경 설정
   각 서비스는 `application.yml` 파일에서 설정을 관리합니다. 기본 구성:
   ```yaml
   server:
     port: 8081  # 각 서비스별로 다른 포트 사용
   
   spring:
     datasource:
       url: jdbc:postgresql://localhost:5432/member # 각 서비스별 DB 이름 변경 필요
       username: admin
       password: Passw0rd
   
   jwt:
     secret-key: '8O2HQ13etL2BWZvYOiWsJ5uWFoLi6NBUG8divYVoCgtHVvlk3dqRksMl16toztDUeBTSIuOOPvHIrYq11G2BwQ'
     access-token-validity: 3600000
     refresh-token-validity: 86400000
   
   allowed-origins: http://localhost:3000
   ```

## 6. 빌드 및 배포

### 6.1 프론트엔드 빌드 및 배포
1. 애플리케이션 빌드
   ```bash
   npm run build
   ```

2. 컨테이너 이미지 빌드
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

3. 이미지 푸시
   ```bash
   docker push {Image Registry주소}/lifesub/lifesub-web:latest
   ```

4. Kubernetes 배포
   ```bash
   kubectl apply -f deployment/manifest/
   ```

### 6.2 백엔드 빌드 및 배포
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

### 6.3 로그인
1) 프론트 페이지 주소 구하기   
```
kubens {namespace}
k get ing
```

2) 로그인 
- ID: user01 ~ user05
- PW: P@ssw0rd$


## 7. 팀

- 오유진 "피오" - Product Owner
- 강동훈 "테키" - Tech Lead
- 김민지 "유엑스" - UX Designer
- 이준혁 "백개" - Backend Developer
- 박소연 "프개" - Frontend Developer
- 최진우 "큐에이" - QA Engineer
- 정해린 "데브옵스" - DevOps Engineer
