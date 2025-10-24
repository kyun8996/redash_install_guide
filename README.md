# AWS EC2에서 Redash 설치 가이드


### 1️⃣ Redash 설치 파일을 내 깃허브에 저장하는 이유

- 개인 프로젝트를 진행하면서 EC2 인스턴스에 Redash를 설치하고 있습니다.  
- EC2 서버 비용때문에 인스턴스를 삭제하거나 재생성하는 경우가 많습니다.  
- 그때마다 Redash를 새로 설치해야 하므로, 설치 명령어와 설정 파일을 정리했습니다.  
- Redash 공식 GitHub(`https://github.com/getredash/setup`) 문서는 종종 업데이트되며,  
  업데이트된 버전에서 더 이상 동일한 설치 방식으로 설치 되지 않을 때가 있습니다.  
- 따라서 **버전 호환성과 재현성 확보를 위해** 제 GitHub 저장소에 설치 파일을 보관했습니다.  
- 이를 통해 언제든지 동일한 환경에서 Redash를 안정적으로 재설치할 수 있습니다.

### 2️⃣ EC2 AMI 권장사항

- **OS 권장 버전 :**  Ubuntu Server **22.04 LTS (x86_64)**   
- **인스턴스 유형 :**  최소 `t3.medium` (vCPU 2개, RAM 4GB 이상)  
- **필수 오픈 포트 :**  `22` (SSH), `80` (HTTP), `443` (HTTPS, 선택) 



### 3️⃣ 설치 명령어

```bash
# 1. 패키지 업데이트
sudo apt update
sudo apt upgrade -y

# 2. Git 설치
sudo apt install git -y

# 3. Redash 설치 스크립트 클론
git clone https://github.com/kyun8996/redash_install_guide.git
cd setup

# 4. 권한 설정
chmod +x setup.sh

# 5. Postgres 데이터 디렉토리 생성 및 권한 설정
# Docker 컨테이너가 호스트 디렉토리를 마운트할 때, 해당 디렉토리가 없거나 접근·쓰기 권한이 없으면 오류가 발생합니다.
sudo mkdir -p /opt/redash/postgres-data
sudo chown -R 1000:1000 /opt/redash/postgres-data
sudo chmod -R 700 /opt/redash/postgres-data

# 6. env 파일 생성
sudo nano /opt/redash/env
[env]
# PostgreSQL 기본 연결 설정
POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=postgre

# Redash가 사용할 데이터베이스 URL
REDASH_DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}

# Redis 연결
REDASH_REDIS_URL=redis://redis:6379/0

# Redash 보안키 (무작위 32자 이상 추천)
REDASH_COOKIE_SECRET=$(openssl rand -hex 16)
REDASH_SECRET_KEY=$(openssl rand -hex 16)

# 7. 설치 실행 (실행할 때 스크립트 파일에서 docker를 자동적으로 설치해준다.)
sudo ./setup.sh
```

### 4️⃣ 저작권 및 라이선스

이 문서는 **Redash 공식 오픈소스 프로젝트**의 설치 스크립트(`getredash/setup`)를 참고하여 작성되었습니다.  
공식 저장소의 설치 스크립트와 문서 일부를 인용하였으며, **개인 학습 및 프로젝트 목적**으로만 사용됩니다.

- 📄 License Information
   - **Original Project:** [Redash](https://github.com/getredash/redash)  
   - **Setup Repository:** [getredash/setup](https://github.com/getredash/setup)  
   - **License Type:** [BSD 2-Clause License](https://github.com/getredash/redash/blob/master/LICENSE)

- 🧾 BSD 2-Clause License Summary