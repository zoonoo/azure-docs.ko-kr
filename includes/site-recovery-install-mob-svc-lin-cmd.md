1. 보호하려는 서버의 로컬 폴더(예: /tmp)에 설치 관리자를 복사합니다. 터미널에서 다음 명령을 실행합니다.
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>모바일 서비스 설치 관리자 명령줄 인수

|매개 변수|형식|설명|가능한 값|
|-|-|-|-|
|-t |필수|에이전트 유형<br>(향후 릴리스에서 사용되지 않음)|*둘 다*|
|지정하지 않을 경우 |필수|에이전트 구성<br>(향후 릴리스에서 사용되지 않음) |*host*|
|-R |옵션|에이전트의 역할|에이전트<br>MasterTarget|
|일시 중지되고 |옵션|모바일 서비스를 설치하는 위치|/usr/local/ASR|
|-i |필수|구성 서버의 IP 주소|모든 유효한 IP 주소|
|-p |필수|구성 서버에서 들어오는 연결을 수신 대기하는 포트|443|
|-s |필수|성공적인 설치 후 서비스 시작<br>(향후 릴리스에서 사용되지 않음)|*y*|
|-c |필수|에이전트와 프로세스 서버 간의 통신 모드<br>(향후 릴리스에서 사용되지 않음) |*https*|
|-P |필수|구성 서버 암호|모든 유효한 UNC 또는 로컬 파일 경로|


#### <a name="example"></a>예
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```
