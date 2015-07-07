<properties
	pageTitle="Azure 백업 - Windows PowerShell | Microsoft Azure를 사용하여 DPM에 대한 백업을 배포하고 관리합니다."
	description="Windows PowerShell을 사용하여 Data Protection Manager(DPM)에 대해 Azure 백업을 배포 및 관리하는 방법을 알아봅니다."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Windows PowerShell을 사용하여 Data Protection Manager(DPM) 서버에 대해 Azure에 백업을 관리하고 배포합니다.
이 문서는 DPM 서버에 Azure 백업을 설정하고 백업과 복구를 관리하기 위해 Windows PowerShell® 명령줄 인터페이스를 사용하는 방법을 보여줍니다.

## Windows PowerShell 환경 설정
Windows PowerShell을 사용하여 Azure 백업을 관리하려면, 올바른 Windows PowerShell 버전과 환경 변수 설정이 필요합니다.

Windows PowerShell 버전 3.0 또는 4.0이 있는지 확인합니다. Windows PowerShell의 버전을 확인하려면 Windows/DPM PowerShell 명령 프롬프트에 다음 명령을 입력합니다.

```
$PSVersionTable
```

다음과 같은 유형의 정보가 제공됩니다.

| 이름 | 값 |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

**PSVersion** 값이 3.0 또는 4.0인지 확인합니다. 그렇지 않으면 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)을 참조하세요.

## 설정 및 등록
### DPM 서버에 Azure 백업 에이전트 설치
Azure 백업 에이전트를 설치하려면, Windows Server에 설치 관리자를 다운로드해 두어야 합니다. 최신 버전의 설치 관리자는 [Microsoft 다운로드 센터](aka.ms/azurebackup_agent)에서 다운로드할 수 있습니다. 쉽게 액세스할 수 있는 위치(예: *C:*.)에 설치 관리자를 저장합니다.

에이전트를 설치하려면 승격된 DPM Windows PowerShell 콘솔에서 다음 명령을 실행합니다.

```
PS C:> MARSAgentInstaller.exe /q
```

그러면 에이전트가 모두 기본 옵션으로 설치됩니다. 설치는 백그라운드에서 몇 분 정도 소요됩니다. */nu* 옵션을 지정하지 않으면 설치 마지막에 Windows 업데이트 창이 열리고 업데이트가 있는지 확인합니다.

설치된 프로그램 목록에 에이전트가 표시됩니다. 설치된 프로그램 목록을 보려면 **제어판** > **프로그램** > **프로그램 및 기능**으로 이동합니다.

![에이전트 설치됨](./media/backup-dpm-automation/installed-agent-listing.png)

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
Azure 백업 서비스에 등록하려면, 다음 필수 조건이 충족되어야 합니다. - 유효한 Azure 구독이 있어야 합니다. - 백업 저장소를 만듭니다. - 저장소 자격 증명을 다운로드하고 편리한 위치(예: *C:\Downloads*)에 저장합니다. 저장소 자격 증명은 편의에 따라 이름을 바꿀 수 있습니다.

[Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787) cmdlet을 사용하여 컴퓨터에 저장소를 등록합니다.

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

그러면 *C:*.에 있는 저장소 자격 증명을 사용하여 “TestingServer”라는 DPM 서버에 Microsoft Azure 저장소가 등록됩니다.

> [AZURE.IMPORTANT]저장소 자격 증명 파일을 지정할 때 상대 경로를 사용하지 마십시오. cmdlet 입력 내용은 반드시 절대 경로를 제공해야 합니다.

### 온라인 백업에 대한 DPM 백업 옵션 구성(구독 설정)
DPM 서버가 온라인 보호에 등록되면 다양한 백업 옵션을 지정하는 기본 구독 옵션으로 설정됩니다. 구독 설정을 변경하려면 기본 설정을 가져온 후 요구 사항에 따라 수정해야 합니다.

#### DPM 서버에 대한 기존 구독 설정 가져오기
DPM 온라인 구독 설정을 구성하려면 우선 [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx) cmdlet을 사용하여 기존 설정을 가져와야 합니다.

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

이 명령을 실행하여 다양한 옵션을 구성하는데 사용될 수 있는 구독 설정 개체를 가져온 후 마지막에 설정을 저장합니다. 새 DPM 서버에 명령을 실행하면 기본 설정이 반환됩니다.

#### 구독 설정 수정
[Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet을 사용하여 DPM 온라인 백업 구독에 대한 다음 설정을 구성할 수 있습니다.

##### 스테이징 영역
DPM 서버에서 실행 중인 Azure 백업 에이전트에는 클라우드로부터 복원된 데이터를 위한 임시 저장소가 필요합니다(로컬 스테이징 영역). 아래 명령을 사용하여 스테이징 영역을 구성할 수 있습니다. 이렇게 하면 스테이징 영역이 **$setting**에 대해 “C:\StagingArea”로 설정되지만 설정을 커밋하지 않으면 DPM 서버에 구성은 적용되지 않습니다.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]위 명령에 지정된 폴더가 이미 존재해야 합니다. 그렇지 않으면 구독 설정을 저장하는 동안 오류 메시지가 나타납니다.

##### 네트워킹
DPM 컴퓨터에서 인터넷으로의 연결은 프록시 서버를 통합니다. 프록시 설정은 에이전트에 제공될 수 있습니다. 이 예제에서는 프록시 서버가 없으므로 프록시와 관련된 모든 정보를 명시적으로 지웁니다.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

대역폭 사용 역시 주의 정해진 요일에 대해 *작업 시간 대역폭*과 *비작업 시간 대역폭* 옵션으로 제어할 수 있습니다. 이 예제에서는 제한을 설정하지 않습니다.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### 암호화 설정
Azure 백업에 전송되는 백업 데이터는 데이터의 기밀성을 보호하기 위해 암호화됩니다. 암호화 암호는 복원 시 데이터를 해독하기 위한 “암호"입니다.

> [AZURE.IMPORTANT]이 정보를 설정한 후에는 안전하게 보관합니다.

아래 예제에서 첫 번째 명령은 passphrase123456789 문자열을 보안 문자열로 변환하고 보안 문자열을 $Passphrase라는 변수에 할당합니다. 두 번째 명령은 암호화 백업의 암호로 $Passphrase에서 보안 문자열을 설정합니다.

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### 구독 설정 저장
*–Commit* 옵션을 사용하여 DPM 서버에 대한 변경 내용을 커밋해야 합니다.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]설정은 커밋하지 않으면 적용되지 않습니다.

## Azure 백업에 데이터 보호
다음 단계에서는 프로덕션 서버를 DPM에 추가하는 방법과 디스크와 Azure 온라인에 데이터를 보호하는 방법을 보여줍니다. 예제에서 파일과 폴더를 백업하는 방법을 보여 줍니다. 논리는 지원되는 모든 데이터 소스를 백업하도록 쉽게 확장될 수 있습니다. 온라인 보호의 필수 구성 요소로 DPM 서버를 설정하고 등록해야 합니다.

모든 DPM 백업은 4개 부분으로 구성되는 보호 그룹(PG)에 의해 제어됩니다.

1. **그룹 멤버**는 동일한 PG 내에서 보호하려는 모든 보호 개체의 목록입니다. 예를 들어, 백업 요구 사항이 다르기 때문에 하나의 PG에서 프로덕션 VM을 보호하고 다른 PG에서 VM을 테스트할 수 있습니다.
2. **데이터 보호 방법**은 데이터를 보호하고자 하는 위치를 지정하는데 사용됩니다. 이 예에서 디스크와 온라인 보호의 데이터를 Azure 클라우드에 보호합니다.
3. 백업을 수행해야 할 때와 DPM 서버 사본과 프로덕션 서버 간의 데이터 동기화 빈도를 지정하는 **백업 일정**입니다.
4. Azure에 복구 지점을 보존할 기간을 지정하는 **보존 일정**입니다.

### 예약된 간격으로 보호 그룹 및 백업 데이터 만들기
#### DPM 서버에 프로덕션 서버 추가
프로덕션 서버에 데이터를 백업할 수 있으려면 DPM 에이전트가 프로덕션 서버에 설치되어 있고 DPM에 의해 관리되는지 확인해야 합니다. DPM 에이전트 설치 및 적절한 DPM 서버에 의해 백업되도록 하는 구성은 [여기](https://technet.microsoft.com/library/bb870935.aspx)의 단계를 따릅니다.

#### PG 만들기
이 문서에서는 백업을 자동화하기 때문에 아무것도 구성되지 않은 것으로 가정합니다. [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx) cmdlet을 사용하여 새 PG를 만듭니다.

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

PG를 변경하려면 [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx) cmdlet을 사용하여 수정할 수 있는 모드로 들어갑니다.

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

위의 cmdlet은 “ProtectGroup01”이라는 PG를 만들지만 아직 백업은 하지 않습니다. 무엇을 백업할지, 언제 백업을 실행할지, 어디에 백업을 저장할지를 정의해야 합니다.

### PG에 그룹 멤버 추가
PG에 데이터 원본을 추가하는 몇 가지 단계가 있습니다.

#### 백업하려는 프로덕션 서버(PS) 가져오기
[Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx) cmdlet을 사용하여 DPM 에이전트가 설치되어 있고 DPM 서버에 의해 관리되고 있는 모든 PS를 가져올 수 있습니다. 이 예제에서 백업에 대해 이름이 “productionserver01”인 PS만 필터링하여 구성하겠습니다.

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### PS에서 데이터 원본 목록을 가져오려면 조회를 실행합니다.
[Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx) cmdlet을 사용하여 DPM이 백업할 수 있는 모든 데이터 원본의 목록을 가져오려면 PS 컴퓨터에서 조회를 실행합니다. 이 예제에서 보호를 구성하기 위해 PS에서 데이터 원본 “D:\”를 필터링할 수 있습니다.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### 보호를 위한 데이터 원본 추가
[Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) cmdlet을 사용하여 데이터 원본을 위 예제에서 생성한 PG “ProtectGroup01”에 추가할 수 있습니다.

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### 데이터 보호 방법 선택
데이터 원본을 PG에 추가하면 보호 방법을 지정해야 합니다. 이 경우 단기 보호는 디스크에 장기 보호는 테이프에 PG를 설정합니다.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### 복구 지점 목표 지정
#### 보존 범위 설정
[Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx)를 사용하여 PG에 대한 보존 범위를 설정할 수 있습니다. 아래 명령은 디스크 기반 복구 지점에 대한 *보존 범위* 및 *동기화 빈도*를 설정합니다.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

온라인 복구 지점에 대해 다양한 GFS 스키마(예: 일간, 주간, 월간, 연간)로 보존 범위를 구성할 수 있습니다. 이 예제에서는 다양한 보존 범위의 개체를 만든 후, 아래 예제에 나오는 것과 같은 개체를 사용하여 온라인 보존 범위를 구성합니다.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### 백업 일정 설정
**Set-PolicyObjective** cmdlet을 사용하여 보호 목표를 지정하면 DPM은 기본 일정을 자동으로 설정합니다. 기본 일정을 변경하려면 [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) cmdlet을 사용한 후 [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx) cmdlet을 사용합니다.

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### 복제본 만들기
데이터 원본을 처음으로 백업하는 경우, DPM은 최초 복제본을 만들어야 하며, 이것은 DPM 복제본 볼륨에서 보호될 데이터 원본의 사본을 생성합니다. [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx) cmdlet을 사용하여 특정 시간에 자동으로 복제본을 만들거나 수동으로 만들도록 예약할 수 있습니다.

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### PG 커밋
마지막으로 [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx) cmdlet을 사용하여 PG 구성마다 DPM에서 백업을 수행하도록 변경 내용을 커밋해야 합니다.

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## 모든 온라인 복구 지점 목록 가져오기
[Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) cmdlet을 사용하여 데이터 원본에 대한 모든 복구 지점 목록을 가져올 수 있습니다. 이 예에서는 - $PG 배열에 저장될 DPM 서버의 모든 PG를 가져옵니다. - $PG[0]에 해당하는 데이터 원본을 가져옵니다. - 데이터 원본에 대한 모든 복구 지점을 가져옵니다.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Azure에 보호된 데이터 복원
이 예는 온라인 복구 지점으로부터 Hyper-V VM을 복원하는 방법을 보여주지만 명령은 데이터 원본 유형에 따라 쉽게 확장될 수 있습니다.

우선 [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx) cmdlet을 사용하여 복구 옵션을 만들어야 합니다. 아래 예제에서는 Hyper-V 데이터 원본을 대체 위치로 복구합니다.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

복구 옵션을 구성한 후에는 위쪽의 [모든 온라인 복구 지점 목록 가져오기](#Getting-a-list-of-all-Online-Recovery-Points) 섹션에서 가져온 온라인 복구 지점을 복원합니다.

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## 다음 단계
DPM에 대한 Azure 백업에 대한 자세한 정보는 [Azure DPM 백업 소개](backup-azure-dpm-introduction.md)를 참조합니다.

<!---HONumber=62-->