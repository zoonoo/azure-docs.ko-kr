1. 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사하고 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 이제 다음 명령줄을 사용하여 모바일 서비스를 설치할 수 있습니다.

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>모바일 서비스 설치 관리자 명령줄 인수

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | 매개 변수|형식|설명|가능한 값|
  |-|-|-|-|
  |/Role|필수|모바일 서비스를 설치해야 하는지 여부를 지정|에이전트 </br> MasterTarget|
  |/InstallLocation|필수|모바일 서비스를 설치하는 위치|컴퓨터의 모든 폴더|
  |/CSIP|필수|구성 서버의 IP 주소| 모든 유효한 IP 주소|
  |/PassphraseFilePath|필수|암호를 저장하는 위치 |모든 유효한 UNC 또는 로컬 파일 경로|
  |/LogGilePath|옵션|설치 로그의 위치|컴퓨터의 모든 유효한 폴더|

#### <a name="sample-usage"></a>샘플 사용

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


