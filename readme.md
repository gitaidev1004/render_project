# 1. Render 활용 기본 사용법

## 1-1. Render 무료 계정 생성 및 기본 설정

### 1-1-1. Render 계정 생성

Render 웹사이트(https://render.com)에 접속하여 Sign Up 버튼을 클릭합니다.
GitHub 또는 GitLab 계정으로 로그인할 수 있습니다. 선택한 계정으로 로그인합니다.

<br><br>

### 1-1-2. 계정 확인 및 대시보드 접근

로그인 후 대시보드에 접근합니다. 대시보드에서 프로젝트를 관리하고 새로운 서비스를 생성할 수 있습니다.

<br><br><br>

## 1-2. Node.js 애플리케이션 생성 및 개발

### 1-2-1. 프로젝트 초기화

**로컬 개발 환경 준비**

터미널에서 다음 명령어를 입력하여 새 디렉토리를 생성하고 초기화합니다.

```bash
mkdir my-render-app
cd my-render-app
npm init -y
```

<br>

**필수 패키지 설치**

Express.js를 사용하여 웹 서버를 생성합니다.

```bash
npm install express
```

<br>

**애플리케이션 코드 작성**

index.js 파일을 생성하고 다음과 같이 작성합니다.

```javascript
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello, Render!');
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

<br>

**기본 파일 구조**

```plaintext
my-render-app/
├── index.js        # 애플리케이션 메인 파일
├── package.json    # 프로젝트 설정 파일
├── package-lock.json # npm 의존성 파일
└── node_modules/   # 설치된 npm 패키지들
```

<br><br>

### 1-2-2. 테스트

**로컬 서버 실행**

```bash
node index.js
```

브라우저에서 http://localhost:3000으로 접속하여 "Hello, Render!" 메시지가 표시되는지 확인합니다.

<br>

**테스트 도구 설치 및 실행 (옵션)**

테스트 도구를 사용하여 자동화된 테스트를 작성할 수 있습니다. 예를 들어, jest를 설치하고 기본 테스트를 작성할 수 있습니다.

```bash
npm install --save-dev jest
```

package.json 파일의 scripts 섹션을 수정하여 test 명령어를 추가합니다.

```json
"scripts": {
  "test": "jest"
}
```

테스트 파일을 __tests__/ 디렉토리에 생성하고 테스트를 작성합니다.

<br><br><br>

## 1-3. Render에 배포

### 1-3-1. GitHub 저장소에 코드 푸시

**Git 초기화 및 커밋**

```bash
git init
git add .
git commit -m "Initial commit"
```

<br>

**GitHub 저장소 생성 및 푸시**

GitHub에서 새로운 저장소를 생성한 후, 아래 명령어로 코드를 푸시합니다.

```bash
git remote add origin https://github.com/username/my-render-app.git
git branch -M main
git push -u origin main
```

<br><br>

### 1-3-2. Render에 애플리케이션 배포

**Render 대시보드에서 새로운 웹 서비스 생성**

대시보드에서 New > Web Service를 선택합니다.

GitHub 저장소를 선택하고, 다음과 같은 배포 설정을 입력합니다.

Build Command: npm install
Start Command: node index.js

<br>

**배포 완료 후 URL 확인**

배포가 완료되면 Render는 애플리케이션의 URL을 제공합니다. 브라우저에서 이 URL로 애플리케이션에 접근할 수 있습니다.

<br><br><br>

## 1-4. 서버리스 함수

### 1-4-1. 서버리스 함수 생성

**서버리스 함수 프로젝트 초기화**

새로운 디렉토리에서 서버리스 함수 프로젝트를 초기화합니다.

```bash
mkdir my-serverless-function
cd my-serverless-function
npm init -y
```

<br>

**서버리스 함수 코드 작성**

handler.js 파일을 생성하고 다음과 같이 작성합니다.

```javascript
module.exports = async (req, res) => {
  res.send('Hello from serverless function!');
};
```

<br>

**서버리스 함수 배포**

Render 대시보드에서 New > Serverless Function을 선택합니다.
GitHub 저장소에서 함수를 선택하고, 적절한 설정을 입력합니다.

<br><br><br>

## 1-5. 데이터베이스 연동 (PostgreSQL 예시)

### 1-5-1. PostgreSQL 데이터베이스 생성

**Render에서 PostgreSQL 데이터베이스 생성**

대시보드에서 New > Database > PostgreSQL을 선택합니다.
데이터베이스 이름, 리전, 플랜을 선택하고 Create Database 버튼을 클릭합니다.

<br>

**연결 정보 확인**

데이터베이스가 생성되면 DATABASE_URL이라는 환경 변수를 제공합니다. 이 값을 복사하여 사용할 수 있습니다.

<br><br>

### 1-5-2. 애플리케이션에서 PostgreSQL 연동

**PostgreSQL 클라이언트 설치**

```bash
npm install pg
```

<br>

**애플리케이션 코드 수정**

index.js 파일을 수정하여 데이터베이스와 연결합니다.

```javascript
const express = require('express');
const { Client } = require('pg');
const app = express();
const port = process.env.PORT || 3000;

const client = new Client({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});

client.connect();

app.get('/', async (req, res) => {
  try {
    const result = await client.query('SELECT NOW()');
    res.send(`Database time: ${result.rows[0].now}`);
  } catch (err) {
    res.status(500).send('Database query error');
  }
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

<br>

**Render에서 환경 변수 설정**

Render 대시보드의 Environment 섹션에서 DATABASE_URL 환경 변수를 설정합니다.

<br>

**배포 및 확인**

변경 사항을 GitHub에 푸시하고, Render 대시보드에서 애플리케이션을 다시 배포합니다.
데이터베이스 연결이 제대로 되는지 확인합니다.

<br><br><br>

## 1-6. 전체 프로젝트 파일 트리 구조

```plaintext
my-render-app/
├── index.js               # Express 서버 코드
├── package.json           # Node.js 프로젝트 설정 파일
├── package-lock.json      # npm 의존성 파일
├── node_modules/          # 설치된 npm 모듈들
└── .gitignore             # Git에서 무시할 파일들

my-serverless-function/
├── handler.js             # 서버리스 함수 코드
├── package.json           # Node.js 프로젝트 설정 파일
├── package-lock.json      # npm 의존성 파일
└── node_modules/          # 설치된 npm 모듈들
```

