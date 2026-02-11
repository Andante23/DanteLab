# WSL 실습 환경 구축하기

ㅁ WSL이란?

  Window Subsystem Linux의 약자이며, ` windows를 그대로 사용하면서 Linux 배포판을 설치해 Bash 명령어와 Linux 도구들을 바로 실행하게 해주는 기술`을 의미합니다.


ㅁ WSL 설치하기 

  `파워쉘`프로그램을 관리자권한으로 실행한다.나머지는 다음 치트시트를 따라가세요

   
  ```powershell

     #1 wsl을 설치한다.
     wsl --install


     #2 wsl를 마이크로소프트 온라인 스토어에서 설치 가능한지 확인합니다.
     wsl.exe --list --online

      NAME                            FRIENDLY NAME
      Ubuntu                          Ubuntu
      Ubuntu-24.04                    Ubuntu 24.04 LTS
      openSUSE-Tumbleweed             openSUSE Tumbleweed
       ....                                   ....
       

      #3 [Distro] 는 #2에서  FRIENDLY NAME을 의미합니다.
      wsl.exe --install [Distro]

      # 리눅스 배포판인 ubuntu 24.04 LTS 를 설치하자
      wsl.exe --install ubuntu 24.04 LTS

      
      #4 하다가 이런 오류를 맞이 하신다면...

      PS C:\Windows\system32> wsl.exe --install Ubuntu-20.04
      Ubuntu 20.04 LTS 시작하는 중...
      Installing, this may take a few minutes...
      WslRegisterDistribution failed with error: 0x80370102
      
      # 여기를 읽어보세요
      # 해석: 윈도우에서 가상 머신 플랫폼을 활성화하고 싶으면, 바이오스에서 가상화를 활성화해주세요 ~
      Please enable the Virtual Machine Platform Windows feature and ensure 
      virtualization   is enabled in the BIOS.
      # 관련된 정보는 해당 URL를 방문하세요.
      For information please visit https://aka.ms/enablevirtualization

      # TIP : 자신의 메인보드의 바이오스에서 가상화를 활성화할 수 있는 방안을 찾아라~ 
      

      # Ubuntu 24.04 LTS 설치중입니다..
      PS C:\Windows\system32> wsl.exe --install Ubuntu-24.04
      다운로드 중: Ubuntu 24.04 LTS
      [====                       7.8%                           ] 


      배포가 설치되었습니다. 'wsl.exe -d Ubuntu-24.04'을(를) 통해 시작할 수 있습니다.
      Ubuntu-24.04 시작하는 중...
      Provisioning the new WSL instance Ubuntu-24.04
      This might take a while...
      # 리눅스 배포판 처음 설치시...
      Create a default Unix user account: 계정이름 추가해주세요

      New password: # 패스워드는 1234 비추천합니다. (실무에서 쓰면 여러분의 소중한 정보 다 털어갑니다. )
      Retype new password:

      
      # /mnt 는 다른 파일시스템을 임시로 마운트(=연결)할 때 사용되는 디렉토리입니다.
      # 지금은 윈도우 파일시스템과 임시 연결되어 있습니다.
      andante@DESKTOP-FFL1JTS:/mnt/c/Windows/system32$

  ```


**※ 참고: 나의 노트북 또는 PC의 메인보드를 알고 싶다면 ?**

     ```bash

      # 출력된 Manufacturer 이름을 구글에 검색할때 바이오스 진입 키도 같이 추가해서 입력해주세요...  

      C:\Windows\system32>wmic baseboard get product,manufacturer,version,serialnumber
                          Manufacturer           Product  SerialNumber     Version
                          ASUSTeK COMPUTER INC.  H81M-K   150954990800371  Rev X.0x
  

        # 저는 f2 혹은 del 키로 확인되었습니다....

        # 추가로 해당 메인보드에서 가상화 기능을 제공하는지 찾아보세요!!!
     
     ``` 

   
    
  

ㅁ 마치며

오늘은 `wsl` 실습 환경을 윈도우10에서 구축해보았습니다. 감사합니다.



      

      
  
      
  


















