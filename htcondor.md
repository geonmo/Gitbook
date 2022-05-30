# HTCondor를 이용한 BIO 연구
UI 서버는 많은 사용자들이 같이 이용하는 서버입니다. 따라서, 컴퓨팅 자원을 많이 사용하는 분석 코드의 경우에는 UI보다는 WN에서 실행하는 것을 추천드립니다.
### 사용방법
* HTCondor 작업을 제출하기 위한 JDS(작업제출명세 파일/.sub 또는 .jds)을 작성합니다.
  In `test.sub`,
   ```
   Universe = vanilla
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
   
   Queue 1
   ```

* 위에서 작성한 작업명세파일을 **condor_submit**으로 제출합니다.
  ```bash
  condor_submit test.sub
  ```