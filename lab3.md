# 자동 시작 프로그램 차단 스크립트 적용기

<br>

> **들어가기 전**

 나는 집에서 FC 온라인이라는 게임을 한다. 게임을 하기 전에 `작업 관리자`를 들어가면 CPU/ 메모리 용량을 너무 잡아먹는다.
 그래서 나는 다음과 같이 줄이는 방안으로 스크립트를 만들어서 `윈도우 작업 스케줄러`에 등록시키고자 한다.

---

<br>

> **파이프라인**

```powershell

  [1] pc 재시작한다. 
  [2] 로그인후 15초 흐른다. 
  [3] 관리자 권한 아니라면 상승하여 실행한다. 
  [4] 그게 아니라면 정책 우회하여 실행한다.
  [5].ps1 파일을 실행한다.
  [6] 정상 실행 - N(그게 아니라면) -> 실패 로그를 남긴다.

```
---

<br>

> **작업스케줄러에 등록하는 방법**

```powershell

# 1. 동작 정의 (실행할 파워쉘 스크립트)
$action = New-ScheduledTaskAction `
  -Execute "PowerShell.exe" `
  -Argument "-File C:\Scripts\MyScript.ps1"

# 2. 트리거 정의 (로그온 시 실행)
# 로그온이란:
# 사용자 계정으로 로그인해서 사용을 시작하는 행위를 의미합니다.
$trigger = New-ScheduledTaskTrigger -AtLogOn


# 3. 작업 등록
Register-ScheduledTask `
  -Action $action `
  -Trigger $trigger `
  -TaskName "MyLogonTask" `
  -Description "로그온 시 파워쉘 스크립트 실행" `
  -User "SYSTEM" `
  -RunLevel Highest

```
---

<br>

> **실패 로그 예시**

 해당 이슈는 다음과 같은 이유로 스크립트에서 제외되었습니다. Windows Defender 실시간 보호 엔진 프로세스라서 일반 관리자 권한으로도 종료가 막혀 있습니다.  

```txt
 2026-02-18 13:59:04 - FAILED to stop: MsMpEng - 다음 오류가 발생하여 "MsMpEng (4596)" 프로세스를 중지할 수 없습니다. 액세스가 거부되었습니다
```

<br>

> **참고하세요**

 <img width="578" height="527" alt="image" src="https://github.com/user-attachments/assets/b1bc0b28-a6f0-45ba-853d-98d5b3d9de63" />
 <br>
 윈도우에 설치된 프로그램들 중에 **자동 실행** 옵션이 있는 경우 비활성화를 해주세요. 그러면 메모리 점유율을 줄일 수 있습니다. 

---

<br>

> **마치며**

  로그파일 경로(path)를 못 찾아서 로그가 기록이 안되는 이슈가 있습니다. 이건 로그파일을 `.ps1` 스크립트에 `$logPath` 변수에 등록한 로그 파일 이름으로 해도 되고, 입맛에 맛게 변경해주셔도 됩니다. 해당 스크립트 파일을 다음 이름(`scipt`)으로 `lab3_ETC` 폴더에 남기겠습니다. .












