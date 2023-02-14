# Python 환경 구축
### pipenv를 이용한 환경 구축
* pipenv는 파이썬 버전 및 virtualenv 환경 그리고 패키지 관리까지 포함된 공식 python 환경 시스템입니다.
* 디렉토리 별로 프로젝트를 구분하기 때문에 새로운 프로젝트는 새로운 디렉토리에서 환경을 구축해야 합니다.

### 초기 환경 구축
1. 신규 디렉토리 생성
    ```bash
    mkdir pipenv_test01
    ```

2. pipenv 설치

    ```bash
    pip3 install --user pipenv
    ```

3. pyenv 설치

    ```bash
    curl https://pyenv.run | bash
    ```

4. pipenv로 python 세팅

    ```bash
    pipenv --python 3.6.8
    ```
    >Creating a virtualenv for this project...\
    Pipfile: /share/geonmo/01management/condor_check/pipenv_test01/Pipfile\
    Using /share/geonmo/.pyenv/versions/3.7.13/bin/python3.7m (3.7.13) to create virtualenv...                                                                    
    ⠏ Creating virtual environment...created virtual environment CPython3.7.13.final.0-64 in 15781ms                                                              
    creator CPython3Posix(dest=/share/geonmo/.local/share/virtualenvs/pipenv_test01-rTDE4siu, clear=False, no_vcs_ignore=False, global=False)              
    seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/share/geonmo/.local/share/virtualenv)               
    added seed packages: pip==22.0.4, setuptools==62.1.0, wheel==0.37.1
    activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator\
    ✔ Successfully created virtual environment!
    Virtualenv location: /share/geonmo/.local/share/virtualenvs/pipenv_test01-rTDE4siu                                                                          
    Creating a Pipfile for this project...

5. pipenv로 패키지 설치
    ```bash
    #pipenv install <Package>
    pipenv install numpy
    ```
    >Installing numpy...\
    Adding numpy to Pipfile's [packages]...\
    ✔ Installation Succeeded\
    Pipfile.lock not found, creating...\
    Locking [dev-packages] dependencies...\
    Locking [packages] dependencies...\
    Building requirements...\
    Resolving dependencies...\
    ✔ Success!\
    Updated Pipfile.lock (2cfc5e)!\
    Installing dependencies from Pipfile.lock (2cfc5e)...\
    🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0  00:00:00\
    To activate this project's virtualenv, run pipenv shell.\
    Alternatively, run a command inside the virtualenv with pipenv run.

6. pipenv shell로 해당 환경 접속
    ```bash
    pipenv shell 
    ```
    >Launching subshell in virtual environment...\
    . /share/geonmo/.local/share/virtualenvs/pipenv_test01-rTDE4siu/bin/activate\
    [geonmo@bio-ui7 pipenv_test01]$  . /share/geonmo/.local/share/virtualenvs/pipenv_test01-rTDE4siu/bin/activate\
    (pipenv_test01) [geonmo@bio-ui7 pipenv_test01]$ pip freeze\
    numpy==1.21.6\
    (pipenv_test01) [geonmo@bio-ui7 pipenv_test01]$ python --version
    Python 3.6.8

7. pipenv run으로 외부에서 명령어 실행 가능
    ```bash
    pipenv run pip freeze
    ```

