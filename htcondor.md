# HTCondor를 이용한 BIO 연구
UI 서버는 많은 사용자들이 같이 이용하는 서버입니다. 따라서, 컴퓨팅 자원을 많이 사용하는 분석 코드의 경우에는 UI보다는 WN에서 실행하는 것을 추천드립니다.
### 작업 제출 방법(condor_submit)
1. HTCondor 작업을 제출하기 위한 JDS(작업제출명세 파일/.sub 또는 .jds)을 작성합니다.
  In `test.sub`,
   ```
   Universe = vanilla
   JobBatchName            = BIO_TEST01
   Log = htcondor.log
   Output = job_log/job_$(Process).out
   Error = job_log/job_$(Process).err
   Accounting_Groups = bio.genome
   
   ### 데이터 전송기능 활성화. singularity 사용시 필수.
   should_transfer_files  = YES
   when_to_transfer_output = ON_EXIT
   
   ### sh> run.script $1 $2 $3
   Executable = run.script
   Arguments = "$1 $2 $3"
   
   ### 전송할 파일 목록
   transfer_input_files = additional_file, 2nd_additional_file, ... 
   transfer_output_files = result.txt
   
   ### 필요한 자원 정보
   Request_cpus = 1
   Request_memory = 5GB
   Request_disk = 1GB
   
   ### 알람 메일 수신 여부
   Notification = Error
   Notify_user = user@email.address
   
   ### 작업 제출.(https://htcondor.readthedocs.io/en/latest/users-manual/submitting-a-job.html)
   Queue 5
   ## Queue 1 A, B from a.txt
   ## Queue 1 in (A, B)
   ```
1. 위에서 작성한 작업명세파일을 **condor_submit**으로 제출합니다.
    ```bash
    condor_submit test.sub
    ```
### 작업 확인(condor_q)
* 제출된 작업의 상태 및 실행 여부를 확인할 수 있는 명령어입니다.
1. condor_q 명령어로 본인의 작업 정보를 확인 가능합니다.
   ```bash
   condor_q
   ````
   > -- Schedd: bio-ui7.sdfarm.kr : <134.75.127.179:9618?... @ 05/30/22 18:44:33
   > 
   > OWNER BATCH_NAME      SUBMITTED   DONE   RUN    IDLE   HOLD  TOTAL JOB_IDS
   > 
   > Total for query: 0 jobs; 0 completed, 0 removed, 0 idle, 0 running, 0 held, 0 suspended\
   > Total for geonmo: 0 jobs; 0 completed, 0 removed, 0 idle, 0 running, 0 held, 0 suspended\
   > Total for all users: 95 jobs; 0 completed, 0 removed, 31 idle, 62 running, 2 held, 0 suspended
   * -allusers(-all) 옵션으로 다른 사용자의 작업을 확인할 수 있습니다.
   * -global 옵션으로 다른 그룹의 작업을 확인할 수 있습니다. 
   * -long(-l) 옵션으로 특정 작업의 자세한 ClassAds 정보를 확인할 수 있습니다.
   * -better-analyze 옵션으로 hold된 작업이나 매칭되는 장비들을 확인할 수 있습니다.

### 서버 상태 확인(condor_status)
* 작동하는 서버들의 작업 할당 여부 등을 확인할 수 있는 명령어입니다.
1. condor_status 명령어로 서버들의 작업 할당 상황을 확인 가능합니다.
   ```bash
   condor_status
   ```
   >   Name                             OpSys      Arch   State     Activity LoadAv Mem     ActvtyTime\
   > slot1@bio-wn3001.sdfarm.kr       LINUX      X86_64 Unclaimed Idle      0.000 229870  0+11:24:04\
   > slot1_1@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      0.830   1024  0+01:11:59\
   > slot1_2@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      1.000   3072  0+00:44:23\
   > slot1_3@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      1.000   1024  0+05:00:39\
   > slot1_4@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      0.990    128  0+07:16:37\
   > slot1_5@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      0.720   1024  0+00:56:09\
   > slot1_6@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      0.990   1024  0+04:29:07\
   > slot1_7@bio-wn3001.sdfarm.kr     LINUX      X86_64 Claimed   Busy      1.150   1024  0+03:10:07
   * State와 Activity에 따라 머신의 상태 확인 가능
     * Unclaimed - Idle : 작업이 할당 안된 슬롯(동적 슬롯이 아닌 경우에는 남은 자원)
     * Claimed - Busy : 작업이 사용 중인 슬롯
   * -server 옵션으로 각 슬롯에 할당된 자원의 크기를 알 수 있습니다.
   * -long(-l) 옵션으로 보다 자세한 ClassAds 정보를 확인할 수 있씁니다.
   
### 작업 우선권 확인(condor_userprio)
* 여러 사용자들의 작업이 제출된 경우 작업의 우선권대로 작업이 실행됩니다. 이에 따라, 같은 그룹 내에서 우선권이 높을수록 더 많은 슬롯이 배정됩니다. 이를 확인하기 위한 명령어입니다.
1. condor_userprio로 우선권 및 제출된 작업 수를 쉽게 파악할 수 있습니다.
   ```bash
   condor_userprio
   ```
   >Last Priority Update:  5/31 08:28\
   >Group                     Config     Use    Effective   Priority   Res   Total Usage  Time Since Requested\
   >User Name                Quota   Surplus   Priority    Factor   In Use (wghted-hrs) Last Usage Resources\
   >------------------------ --------- ------- ------------ --------- ------ ------------ ---------- ----------\
   >group_genome.bio           1696.00 ByQuota                1000.00    437   7307860.00      <now>        437\
   >cslee159@sdfarm.kr                           93526.77   1000.00     48   1529346.88      <now>           \
   >qeip2004@sdfarm.kr                          221691.70   1000.00     64   2673090.00      <now>           \
   >lagom2728@sdfarm.kr                         414466.09   1000.00    325    512224.19      <now>           \
   >group_alice                1160.00 ByQuota                1000.00   1526  12342982.00      <now>       3521\
   >shlim@sdfarm.kr                             983820.12   1000.00   1526   3373940.50      <now>           \
   >group_cms                  2288.00 ByQuota                1000.00   3170  23268910.00      <now>       4920\
   >chdlalsnr@sdfarm.kr                           8019.85   1000.00      8    125969.51      <now>           \
   >sacharya@sdfarm.kr                           11036.04   1000.00      2      6799.54      <now>           \
   >taebh@sdfarm.kr                              71342.91   1000.00      8    206882.72      <now>           \
   >runiyal@sdfarm.kr                           136847.88   1000.00     29   1641918.25      <now>           \
   >jhchoi@sdfarm.kr                            227966.92   1000.00    301   8448762.00      <now>           \
   >yhoonlee@sdfarm.kr                          417066.94   1000.00     13    267412.28      <now>           \
   >wjjang@sdfarm.kr                           2321194.25   1000.00   2809   3550416.25      <now>\
   >------------------------ --------- ------- ------------ --------- ------ ------------ ---------- ----------\
   >Number of users: 11                ByQuota                          5133  22336762.00    0+23:59           \
   * 그룹(group_genome.bio) 쿼터(1696)의 숫자와 수행 중인 작업 수(437) 및 제출된 작업 수(437) 정보등을 확인할 수 있습니다.
   
### 작업 예시

   


