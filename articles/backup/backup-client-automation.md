<properties
	pageTitle="Windows PowerShell | Microsoft Azure를 사용하여 Windows Server/Client에 대한 백업을 배포하고 관리합니다."
	description="Windows PowerShell을 사용하여 Azure 백업을 배포 및 관리하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Windows PowerShell을 사용하여 Windows Server/Windows Client용 Azure 백업 배포 및 관리
이 문서는 Windows Server 또는 Windows Client에 Azure 백업을 설정하고 백업과 복원을 관리하기 위한 Windows PowerShell® 명령줄 인터페이스 사용법을 보여줍니다.

## Windows PowerShell 환경 설정
Windows PowerShell을 사용하여 Azure 백업을 관리하려면, 올바른 Windows PowerShell 버전과 환경 변수 설정이 필요합니다.

## 설정 및 등록
다음과 같은 설정 및 등록 작업은 Windows PowerShell에서 자동화될 수 있습니다. - Azure 백업 에이전트 설치 - Azure 백업 서비스 등록 - 네트워킹

### Azure 백업 에이전트 설치
Azure 백업 에이전트를 설치하려면, Windows Server에 설치 관리자를 다운로드해 두어야 합니다. 최신 버전의 설치 관리자는 [Microsoft 다운로드 센터](aka.ms/azurebackup_agent)에서 다운로드할 수 있습니다. 쉽게 액세스할 수 있는 위치(예: *C:\Downloads*)에 설치 관리자를 저장합니다. 에이전트를 설치하려면 승격된 Windows PowerShell 콘솔에서 다음 명령을 실행합니다.

```
PS C:> MARSAgentInstaller.exe /q
```

그러면 에이전트가 모두 기본 옵션으로 설치됩니다. 설치는 백그라운드에서 몇 분 정도 소요됩니다. **/nu** 옵션을 지정하지 않으면 설치 마지막에 **Windows 업데이트** 창이 열리고 업데이트가 있는지 확인합니다.

설치된 프로그램 목록에 에이전트가 표시됩니다. 설치된 프로그램 목록을 보려면 **제어판** > **프로그램** > **프로그램 및 기능**으로 이동합니다.

![에이전트 설치됨](./media/backup-client-automation/installed-agent-listing.png)

#### 설치 옵션

명령줄을 통해 사용 가능한 모든 옵션을 보려면 다음 명령을 사용합니다.

```
PS C:> MARSAgentInstaller.exe /?
```

사용 가능한 옵션은 다음과 같습니다.

| 옵션 | 세부 정보 | 기본값 |
| ---- | ----- | ----- |
| /q | 무인 설치 | - | | /p:"location" | Azure 백업 에이전트에 대한 설치 폴더 경로. | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"location" | Azure 백업 에이전트에 대한 캐시 폴더 경로. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Opt-in to Microsoft Update | - | | /nu | 설치 완료 후 업데이트 확인 안 함 | - | | /d | Microsoft Azure 복구 서비스 에이전트 제거 | - | | /ph | 프록시 호스트 주소 | - | | /po | 프록시 호스트 포트 번호 | - | | /pu | 프록시 호스트 사용자 이름 | - | | /pw | 프록시 암호 | - |


### Azure 백업 서비스 등록
Azure 백업 서비스에 등록하려면, [필수 조건](backup-try-azure-backup-in-10-mins.md)이 충족되어야 합니다. - 유효한 Azure 구독이 있어야 합니다. - 백업 저장소를 만듭니다. - 저장소 자격 증명을 다운로드하고 편리한 위치(예: *C:\Downloads*)에 저장합니다. 저장소 자격 증명은 편의에 따라 이름을 바꿀 수 있습니다.

[Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet을 사용하여 컴퓨터에 저장소를 등록합니다.

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]저장소 자격 증명 파일을 지정할 때 상대 경로를 사용하지 마십시오. cmdlet 입력 내용은 반드시 절대 경로를 제공해야 합니다.


### 네트워킹
Windows 컴퓨터의 인터넷 연결이 프록시 서버를 통하는 경우, 프록시 설정도 에이전트에 제공될 수 있습니다. 이 예제에서는 프록시 서버가 없으므로 프록시와 관련된 모든 정보를 명시적으로 지웁니다.

대역폭 사용 역시 주의 정해진 요일에 대해 *작업 시간 대역폭*과 *비작업 시간 대역폭* 옵션으로 제어할 수 있습니다.

프록시 및 대역폭 세부 정보 설정은 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet을 사용합니다.

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### 암호화 설정
Azure 백업에 전송되는 백업 데이터는 데이터의 기밀성을 보호하기 위해 암호화됩니다. 암호화 암호는 복원 시 데이터를 해독하기 위한 “암호"입니다. 이 정보를 설정한 후에 안전하게 보관하는 것이 중요합니다.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Azure 백업 에이전트 제거
Azure 백업 에이전트 제거는 다음 명령을 사용하여 수행할 수 있습니다.

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

컴퓨터에서 에이전트 이진 파일을 제거하면 다른 결과가 발생합니다. - 컴퓨터에서 파일 필터를 제거하고 변경 내용 추적이 중단됩니다. - 모든 정책 정보가 컴퓨터에서 제거되지만 정책 정보는 서비스에 계속 저장됩니다. - 모든 백업 일정이 제거되고 더 이상 백업이 수행되지 않습니다.

하지만 Azure에 저장된 데이터는 계속 유지되며 사용자가 설정한 보존 정책에 따라 보존됩니다. 이전 지점은 시간이 경과하면 자동으로 삭제됩니다.

## 원격 관리
Azure 백업 에이전트, 정책, 데이터 소스와 관련된 모든 관리는 Windows PowerShell을 통해 원격으로 수행될 수 있습니다. 원격으로 관리될 컴퓨터는 올바르게 준비되어야 합니다.

기본적으로 WinRM 서비스는 수동 시작으로 구성됩니다. 시작 유형은 반드시 *자동*으로 설정되어야 하며 서비스가 시작되어야 합니다. WinRM 서비스가 실행되는지 확인하도록 Status 속성의 값은 *Running*이어야 합니다.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

이제 에이전트 설치부터 시작하여 컴퓨터를 원격으로 관리할 수 있습니다. 예를 들어, 다음 스크립트는 에이전트를 원격 컴퓨터로 복사하고 설치합니다.

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## 다음 단계
Windows Server/Client용 Azure 백업에 대한 자세한 정보는 [Azure 백업 소개](backup-introduction-to-azure-backup.md)를 참조합니다.

<!---HONumber=62-->