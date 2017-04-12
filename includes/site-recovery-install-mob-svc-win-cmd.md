1. 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사합니다. 명령 프롬프트에서 관리자 권한으로 다음 명령을 실행합니다.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>모바일 서비스 설치 관리자 명령줄 인수

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | 매개 변수를 포함해야 합니다.|형식|설명|가능한 값|
  |-|-|-|-|
  |/Role|필수|모바일 서비스를 설치해야 하는지 여부를 지정합니다.|에이전트 </br> MasterTarget|
  |/InstallLocation|필수|모바일 서비스를 설치하는 위치|컴퓨터의 모든 폴더|
  |/CSIP|필수|구성 서버의 IP 주소| 모든 유효한 IP 주소|
  |/PassphraseFilePath|필수|암호의 위치 |모든 유효한 UNC 또는 로컬 파일 경로|
  |/LogFilePath|옵션|설치 로그의 위치|컴퓨터의 모든 유효한 폴더|

#### <a name="example"></a>예

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
