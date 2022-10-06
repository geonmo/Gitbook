# BioBakery 패키지 관련 설치 안내

## 관련사항

* BioBakery 패키지들 대부분은 ctypes 모듈이 필요하므로 기본 python 버전(3.6.8)으로 설치해야 함

## 설치방법

1. pipenv 설치
   ```bash
   pip3 install pipenv --user
   ```

2. workspace 디렉토리 생성 및 이동

   ```bash
   mkdir mpa
   cd mpa
   ```

3. pipenv 환경설정 및 metaphlan,humann 패키지 설치

   ```bash
   pipenv --python 3.6.8
   pipenv install metaphlan
   pipenv install humann
   ```

4. DB 설정
   ```bash
   pipenv run humann_config --update database_folders utility_mapping /bio/lib/databases/utility_mapping
   echo "DEFAULT_DB_FOLDER=/bio/lib/databases/metaphlan_databases/" >> .env
   ```

## 사용법

* HTCondor Exectable 용 bash script에 다음 내용 포함
  ```bash
  cd $HOME/mpa
  #pipenv run [실행할 명령어]
  pipenv run metaphlan ~~~~
  ```

  