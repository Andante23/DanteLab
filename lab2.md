# ssh 환경 구축하기

**ㅁ 구축하는 이유**

  개발을 하다 보면 서버에 접속을 해야될 때가 있다. 그런데, 네가 위치한 곳이 카페라면 그 서버에 원격으로 접속해야 될 것이다. 그런 상황을 위해서 나온 것이 `ssh` 이다. 


**ㅁ ssh 서버 설치/설정**

```powershell

#1.  우선 wsl에 접속해주자. 해당 ssh 서버 설치를 위해 다음 명령어를 입력하자.
# openssh-server를 패키지에서 참조하여 설치한다. 최신 버전 반영을 위해 다음 명령어 우선 입력해주자
sudo apt update # 먼저
sudo apt install openssh-server


#2.  Ubuntu 15.04 이후부터 init → systemd 로 변경되면서
# 시스템 조회 명령어도 달라졌다,
sudo systemctl ssh status  # ssh 서비스 조회 명령어

#3. 지금은 ssh 서비스가 죽어있는 상태이다.
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: man:sshd(8)
             man:sshd_config(5)

#4. ssh 서비스를 시작하는 명령어이다. 다시 조회하자
andante@DESKTOP-FFL1JTS:~$ sudo systemctl start ssh     
andante@DESKTOP-FFL1JTS:~$ sudo systemctl status ssh    
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; disabled; vendor preset: enabled)
     Active: active (running) since Thu 2026-02-12 14:49:43 KST; 2s ago   # 방금 서비스가 시작했음을 알 수 있다.
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 2054 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 2055 (sshd)
      Tasks: 1 (limit: 4646)
     Memory: 5.3M
        CPU: 20ms
     CGroup: /system.slice/ssh.service
             └─2055 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

Feb 12 14:49:43 DESKTOP-FFL1JTS systemd[1]: Starting OpenBSD Secure Shell server...
# 한 가지 더 볼 것은 ssh 서비스는 UDP 22번 포트에서 실행된다....
Feb 12 14:49:43 DESKTOP-FFL1JTS sshd[2055]: Server listening on 0.0.0.0 port 22.
Feb 12 14:49:43 DESKTOP-FFL1JTS sshd[2055]: Server listening on :: port 22.
Feb 12 14:49:43 DESKTOP-FFL1JTS systemd[1]: Started OpenBSD Secure Shell server.


 #5. ssh 설정하기

# ssh 설정파일 들어갑니다.
andante@DESKTOP-FFL1JTS:~$ sudo vi /etc/ssh/sshd_config

# port 22
  port 22 로 주석해제하기

 # 설정 반영을 위해 ssh 서비스 재시작합니다.
   $ sudo systemctl  restart ssh
 # ssh 22번 포트 상태 값이 LISTEN인지 체크하기
    
   $ sudo ss -tlnp | grep 22
   
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN  # 넘어가기
    tcp6       0      0 :::22                   :::*                    LISTEN


  #6. 서버에 원격접속되는지 확인하기
  
   # ssh 이용하기 ( 예시: ssh 사용자명@<원격접속_서버_ip> -p <ssh_포트_넘버> )
   ssh andante@서버_ip
   andante@172.31.38.83: Permission denied (publickey).
   # ssh에 들어가려면 공개키가 있어야 되는데 없어서 들어가지 못한다 입니다.


   #7. ssh 서버 접속키 만들기 
   # 사용 예시: ssh-keygen -t <암호화 알고리즘> -b <암호화 비트>
   
   $ ssh-keygen -t rsa -b 4096
     
      Generating public/private rsa key pair.
      Enter file in which to save the key (/home/andante/.ssh/id_rsa): yes
      Enter passphrase (empty for no passphrase):
      Enter same passphrase again:

       # 님의 키가 성공적으로 생성되었다는 것을 의미합니다. 
       Your identification has been saved in yes
       Your public key has been saved in yes.pub
       The key fingerprint is:
        SHA256:<ssh_인증_키> andante@DESKTOP-FFL1JTS
         The keys randomart image is:
           

    #8. 키 인증 로그인을 위해서 해당 공개 키를 파일에 추가합니다.
    $ mkdir -p ~/.ssh
    $ chmod 700 ~/.ssh
    $ echo <ssh_인증_키> >> ~/.ssh/authorized_keys
    $ chmod 600 ~/.ssh/authorized_keys

```
 

**ㅁ Windows 방화벽에서 포트 허용**

  WSL은 Windows 네트워크 스택 위에서 동작하므로 접속을 허용하려면 Windows 방화벽 인바운드 규칙을 추가해야 합니다. (LAB2_ETC 폴더에 인바운드 규칙허용.PDF 파일로 추가했습니다.)



**ㅁ ssh로 wsl 접속 테스트**

```powershell
   
   PS C:\Windows\system32> ssh andante@172.31.38.83 -p 22
   andante@172.31.38.83: Permission denied (publickey).

     # 서버가 publickey 인증만 허용하는데 클라이언트가 키를 제공하지 못했거나
     # 서버 authorized_keys에 등록 안 됐을 때 발생합니다.
     
     #  설정파일에 확인해보니 인증 옵션이 주석처리 되어있었습니다.
     #  저는 키 인증을 통해 접속하겠습니다.

   #LoginGraceTime 2m
   #PermitRootLogin prohibit-password
   #StrictModes yes
   #MaxAuthTries 6
   #MaxSessions 10

   PubkeyAuthentication yes  # 해당 옵션을 주석 해제해주세요
   
   # 설정 적용을 위해 ssh 서비스를 재시작합니다.
   $ sudo systemctl  restart ssh

```

 ssh를 이용하여 윈도우 파워쉘에서 원격접속에 성공하였습니다.

![alt text](lab2_ETC/image.png)






 








  

  
  




