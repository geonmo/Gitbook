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

1. 이후 패키지 설치를 위한 R파일을 작성합니다. 작성 후 이미지로 접속을 합니다.

{% code title="package_install.R" %}
```bash
## 컨테이너 내부에 쓰기가 불가능하기 때문에 라이브러리를 저장할 디렉토리를 지정해야 합니다.         lib_path <-"/share/geonmo/TOOLS/Rlibrary_singularity"                                     
.libPaths(lib_path)                                                                       
if(!requireNamespace("BiocManager", quietly = TRUE))                                        install.packages("BiocManager", lib=lib_path)                                         BiocManager::install("Maaslin2",quietly=TRUE, lib=lib_path)
```
{% endcode %}

\## Login to bioconductor image

2

singularity shell -i -p bioconductor\_docker\_RELEASE\_3\_15.sif

Copied!

\3. 해당 파일을 아래와 같이 실행합니다.

1

Rscript package\_install.R

Copied!

\4. 패키지 설치를 홈디렉토리로 지정을 하였다면 모든 WN에서 접근이 가능합니다. 해당 Singularity 실행환경을 위해 아래와 같이 condor 제출파일과 bash 스크립트 파일을 작성합니다.

submit\_singularity.jdl

1

JobBatchName = BioCondctor\_Package\_Installation\_Test\_$(Cluster)

2

executable = singularity\_test.sh

3

universe = vanilla

4

requirements = ( HasSingularity == true )

5

getenv = True

6

should\_transfer\_files = YES

7

when\_to\_transfer\_output = ON\_EXIT

8

log = $(JobBatchName).log

9

error = job\_$(Process).stderr

10

output = job\_$(Process).stdout

11

12

transfer\_input\_files = singularity\_test.sh, tmao.set.R

13

\#transfer\_output\_files =

14

15

request\_cpus= 1

16

request\_memory= 3G

17

request\_disk = 1G

18

19

20

accounting\_group=group\_genome.bio

21

\## 다운로드된 이미지의 절대결로를 넣어주세요.

22

\+SingularityImage = "<절대경로>/bioconductor\_docker\_RELEASE\_3\_15.sif"

23

\+SingularityBind = "/cvmfs, /cms, /bio, /epig, /share"

24

25

\#notification = Error

26

\#notify\_user =

27

28

queue 1

Copied!

singularity\_test.sh

1

\#!/bin/bash

2

3

/usr/local/bin/Rscript tmao.set.R

Copied!

\5. HTCondor로 작업을 제출한 후 기다려봅니다.

1

condor\_submit submit\_singularity.jdl
