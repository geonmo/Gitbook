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
   [geonmo@bio-ui7 ~]$ condor_q
   -- Schedd: bio-ui7.sdfarm.kr : <134.75.127.179:9618?... @ 05/30/22 18:44:33
   OWNER BATCH_NAME      SUBMITTED   DONE   RUN    IDLE   HOLD  TOTAL JOB_IDS
   
   Total for query: 0 jobs; 0 completed, 0 removed, 0 idle, 0 running, 0 held, 0 suspended
   Total for geonmo: 0 jobs; 0 completed, 0 removed, 0 idle, 0 running, 0 held, 0 suspended
   Total for all users: 95 jobs; 0 completed, 0 removed, 31 idle, 62 running, 2 held, 0 suspended
    ```
   * -allusers(-all) 옵션으로 다른 사용자의 작업을 확인할 수 있습니다.
   * -global 옵션으로 다른 그룹의 작업을 확인할 수 있습니다. 
   * -long(-l) 옵션으로 특정 작업의 자세한 ClassAds 정보를 확인할 수 있습니다.
   * -better-analyze 옵션으로 hold된 작업이나 매칭되는 장비들을 확인할 수 있습니다.