# 실행 정책 우회 재실행
if ($env:PSExecutionPolicyPreference -ne "Bypass") {
    Start-Process powershell -ArgumentList "-ExecutionPolicy Bypass -NoProfile -File `"$PSCommandPath`"" -Verb RunAs
    exit
}

# 관리자 권한 확인
if (-not ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()
).IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)) {
    
    #해당 Role이 관리자 권한이 아니라면 관리자 권한으로 상승합니다.
    
    Start-Process powershell -ArgumentList "-ExecutionPolicy Bypass -NoProfile -File `"$PSCommandPath`"" -Verb RunAs
    exit
}

# 15초 뒤에 실행
Start-Sleep -Seconds 15

# 로그 파일 경로
$logPath = "C:\Users\andante\Desktop\kill_autostart_log.txt"

# 자동 시작 프로그램 차단 항목들
$processTargets = @(

    "MicrosoftEdgeUpdate",
    " 차단할 서비스명1 ",
    " 차단할 서비스명2 "
)

# 프로세스 타겟을 루프를 돌음
foreach ($p in $processTargets) {
    $proc = Get-Process -Name $p -ErrorAction SilentlyContinue
    if ($proc) {
        try {
            # 해당 타켓이 존재하면 정지하기
            $proc | Stop-Process -Force -ErrorAction Stop
        }
        catch {

            # 만약 타켓을 발견하지 못한다면 다음과 같은 로그를 남김니다.
            $time = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
            Add-Content -Path $logPath -Value "$time - FAILED to stop: $p - $($_.Exception.Message)"
        }
    }
}
