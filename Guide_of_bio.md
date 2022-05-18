# 유전체 활용법

## BioConductor 사용을 위한 환경 설정

통합팜 구성이 CentOS7으로 되어 있기 때문에 Bioconductor가 정상적으로 설치되지 않습니다. 이를 위해 조치가 필요합니다.

### 기본 개념

Docker와 유사한 프로그램인 Singularity를 이용하여 자신만의 Bioconductor 환경을 구성합니다.

## 사용방법

1. bio-ui7 서버에서 Singularity 이미지를 다운로드합니다. 해당 이미지는 docker 이미지를 이용합니다.
   ```bash
   ## Singularity is already installed on bio-ui7.
   ## Download bioconductor docker image
   singularity pull docker://bioconductor/bioconductor_docker:RELEASE_3_15
   ```

2. 이후 패키지 설치를 위한 R파일을 작성합니다. 작성 후 이미지로 접속을 합니다.

   {% code title="package_install.R" %} 

   ```bash
   ## 컨테이너 내부에 쓰기가 불가능하기 때문에 라이브러리를 저장할 디렉토리를 지정해야 합니다.
   lib_path <-"/share/geonmo/TOOLS/Rlibrary_singularity"
   .libPaths(lib_path)
   if(!requireNamespace("BiocManager", quietly = TRUE))
       install.packages("BiocManager", lib=lib_path)
   BiocManager::install("Maaslin2",quietly=TRUE, lib=lib_path)
   ```

   {% endcode %}

3. 전송된 이미지 파일은 아래 명령어로 접속하여 확인이 가능합니다.
   ```bash
   ##Login to bioconductor image
   singularity shell -i -p bioconductor_docker_RELEASE_3_15.sif
   ```

4. 해당 파일을 아래와 같이 실행합니다.
   ```bash
   Rscript package\_install.R
   ```

5. 패키지 설치를 홈디렉토리로 지정을 하였다면 모든 WN에서 접근이 가능합니다. 해당 Singularity 실행환경을 위해 아래와 같이 condor 제출파일과 bash 스크립트 파일을 작성합니다.
   {% code title="submit\_singularity.jdl" %}

   ```
   JobBatchName = BioCondctor\_Package\_Installation\_Test\_$(Cluster)
   executable = singularity\_test.sh
   universe = vanilla
   requirements = ( HasSingularity == true )
   getenv = True
   should\_transfer\_files = YES
   when\_to\_transfer\_output = ON\_EXIT
   log = $(JobBatchName).log
   error = job\_$(Process).stderr
   output = job\_$(Process).stdout
   
   transfer\_input\_files = singularity\_test.sh, tmao.set.R
   request\_cpus= 1
   request\_memory= 3G
   request\_disk = 1G
   accounting\_group=group\_genome.bio
   ## 다운로드된 이미지의 절대결로를 넣어주세요.
   +SingularityImage = "<절대경로>/bioconductor\_docker\_RELEASE\_3\_15.sif"
   +SingularityBind = "/cvmfs, /cms, /bio, /epig, /share"
   #notification = Error
   #notify\_user =
   
   queue 1
   ```

   {% code title="singularity\_test.sh" %}
   ```bash
   #!/bin/bash
   /usr/local/bin/Rscript tmao.set.R
   ```

   {% endcode %}

5. HTCondor로 작업을 제출한 후 기다려봅니다.
   ```bash
   condor\_submit submit\_singularity.jdl
   ```

   

