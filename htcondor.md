# HTCondor를 이용한 BIO 연구
UI 서버는 많은 사용자들이 같이 이용하는 서버입니다. 따라서, 컴퓨팅 자원을 많이 사용하는 분석 코드의 경우에는 UI보다는 WN에서 실행하는 것을 추천드립니다.
### 사용방법
* HTCondor 작업을 제출하기 위한 JDS(작업제출명세 파일/.sub 또는 .jds)을 작성합니다.
   ```
   Universe = vanilla
   Log = htcondor.log
   Output = job_log/job_$(Process).out
   Error = job_log/job_$(Process).err
   Accounting_Groups = bio.genome
   
   should_transfer_files  = YES
   when_to_transfer_output = ON_EXIT
   
   Executable = run.script
   Arguments = "$1 $2 $3"
   transfer_input_files = additional_file, 2nd_additional_file, ... 
   transfer_output_files = result.txt
   
   Request_cpus = 1
   Request_memory = 5GB
   Request_disk = 1GB
   
   Notification = Error
   Notify_user = user@email.address
   
   Queue 1
   ```
   
hello, dfd