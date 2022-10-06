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
   mkdir $HOME/mpa
   cd $HOME/mpa
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
  #pipenv run [실행할 명령어] 혹은 pipenv shell로 접속 후 실행
  pipenv run metaphlan ~~~~
  ```

## 참고사항

* metaphlan의 경우, ```--bowtie2db``` 옵션으로 DB 위치 지정이 가능합니다. 
  ```DEFAULT_DB_FOLDER``` 환경변수가 작동하지 않는다면 해당 옵션으로 DB 디렉토리 지정이 필요합니다.
* DB를 /bio 디렉토리에 지정하면 DB 업데이트를 1번만 해도 되기 때문에 용량 문제가 발생하지 않습니다. 
  * 현재 최신 metaphlan의 경우 DB 파일이 50GB가 넘어가기 때문에 되도록 다른 application 사용시에도 DB 디렉토리를 되도록 /bio로 해서 사용해주시기를 요청드립니다. 