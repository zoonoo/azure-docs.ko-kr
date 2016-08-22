<properties
	pageTitle="Azure 자동화 DSC와 함께 Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 복제 | Microsoft Azure"
	description="Azure 자동화 DSC를 사용하여 가상/물리적 컴퓨터에 대한 ASR 모바일 서비스 및 Azure 에이전트를 Azure에 자동으로 배포하는 방법을 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Azure 자동화 DSC와 함께 Site Recovery를 사용하여 Azure에 VMware VM 복제

OMS에서는 비즈니스 연속성 계획의 일부로 활용할 수 있는 포괄적인 백업 및 재해 복구 솔루션을 제공합니다.

이 과정은 Hyper-V 복제본을 사용하여 Hyper-V와 함께 시작했지만 고객들이 클라우드에 여러 하이퍼바이저 및 플랫폼을 가지고 있으므로 다른 유형 설정을 지원하도록 확장했습니다.

VMware 워크로드 또는/및 현재 물리적 서버를 실행 중인 경우 고객의 환경에서 실행 중인 모든 Site Recovery 구성 요소를 실행하는 관리 서버를 기반으로 Azure가 대상인 경우 Azure를 사용하여 통신 및 데이터 복제본을 처리합니다.

## OMS 자동화 DSC를 사용하여 ASR 모바일 서비스 배포
먼저 이 관리 서버가 실제로 수행하는 작업을 빨리 분석해 보겠습니다.

관리 서버는 통신을 조정하고 데이터 복제본 및 복구 프로세스를 관리하는 **구성**과 같은 여러 서버 역할을 실행합니다.

또한 **프로세스** 역할은 복제본 게이트웨이로 동작하여 보호된 원본 컴퓨터에서 복제본 데이터를 수신하고 이 데이터를 Azure Storage 계정에 보내기 전에 캐싱, 압축 및 암호화를 사용하여 최적화합니다. 보호된 컴퓨터에 대한 모바일 서비스의 푸시 설치를 실행하고 VMware VM의 자동 검색을 수행하는 것도 프로세스 역할에 대한 기능 중 하나입니다.

Azure에서 장애가 발생한 경우 **마스터 대상** 역할이 책임을 지며 이 작업의 일부로 복제본 데이터를 처리합니다.

보호된 컴퓨터의 경우 Azure에 복제할 모든 컴퓨터(VMware VM 또는 물리적 서버)에 배포된 구성 요소인 **모바일 서비스**를 기반으로 합니다. 이 서비스의 역할은 컴퓨터의 데이터 쓰기를 캡처하여 관리 서버에 전달하는 것입니다(프로세스 역할).

비즈니스 연속성을 처리하는 경우 워크로드, 인프라 및 RTO와 RPO에 대한 요구 사항을 만족하기 위해 관련된 구성 요소를 이해해야 합니다. 이 컨텍스트에서 모바일 서비스를 사용하면 워크로드가 예상한 대로 보호됩니다.

그렇다면 어떻게 해서 일부 OMS 구성 요소를 이용하여 최적의 방법으로 신뢰할 수 있는 보호된 설정을 확보할 수 있습니까?

이 문서에서는 OMS Site Recovery와 함께 OMS 자동화 DSC를 활용하여 모바일 서비스 및 Azure VM 에이전트를 보호하려는 Windows 컴퓨터에 배포하고 Azure가 복제 대상인 경우 항상 실행하도록 할 수 있는 방법에 관한 예제를 제공합니다.

## 필수 조건

- 필수 설치 프로그램을 저장할 저장소
- 관리 서버에 등록하기 위해 필요한 암호를 저장할 저장소
- 보호를 위해 활성화하려는 컴퓨터에 Windows 관리 프레임 워크 5.0 설치(OMS 자동화 DSC에 대한 요구 사항)

WMF 4.0을 사용하여 Windows 컴퓨터에 대해 DSC를 사용하려는 경우 '연결이 끊어진 환경에서 DSC 사용'에 대한 섹션을 참조

(각 관리 서버에 대해 고유한 암호가 생성됩니다. 여러 관리 서버를 배포하려는 경우 올바른 암호가 passphrase.txt 파일에 저장 되어 있는지 확인해야 합니다.)

모바일 서비스는 명령줄을 통해 설치할 수 있으며 여러 인수를 허용합니다.

따라서 이진 파일(우리의 설치 프로그램에서 추출한 후)을 가져와서 DSC 구성을 사용하여 검색할 수 있는 위치에 저장해야 합니다.

## 1단계: 이진 파일 추출

1. 이 설치 프로그램을 위해 필요한 파일을 추출하려면 관리 서버에서 다음 디렉터리로 이동: **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    이 폴더에 다음 이름의 MSI 파일이 표시됩니다.

    **Microsoft-ASR\_UA\_<version>_Windows\_GA_<date>\_Release.exe**

    다음 cmdlet을 사용하여 설치 관리자를 추출합니다.

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. 모든 파일을 선택하고 압축된(zip) 폴더에 보냅니다.

끝났습니다. 이제 OMS 자동화 DSC를 사용하여 모바일 서비스의 설치를 자동화하는 데 필요한 이진 파일이 준비되었습니다.

### 암호
다음으로, 이 압축된 폴더를 배치할 위치를 결정해야 합니다. 필자의 경우 나중에 Azure에서 저장소 계정을 사용하고 에이전트가 프로세스의 일부로 관리 서버에 등록하도록 설치 프로그램을 위해 필요한 암호도 이 계정에 저장하는 것을 보여 줍니다.

관리 서버를 배포할 때 얻은 암호를 passphrase.txt에 텍스트 파일로 저장할 수 있습니다.

필자는 zip 폴더와 암호를 모두 Azure Storage 계정의 전용 컨테이너에 배치했습니다.

![폴더 위치](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

이 파일들을 네트워크 내의 공유 폴더에 보관할 수도 있습니다. 나중에 실제로 사용할 DSC 리소스에 대한 액세스 권한이 있고 설치 프로그램과 암호를 가져올 수 있는지 확인하기만 하면 됩니다.

## 2단계 - DSC 구성 만들기
필자의 설정은 WMF 5.0에 따라 달라지므로 컴퓨터가 OMS 자동화 DSC를 통해 구성을 적용하려면 WMF 5.0이 있어야 합니다.

다음과 같은 DSC 구성을 필자의 환경에 사용합니다.

```
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
```
이 구성은 다음을 수행합니다.

- 변수를 통해 모바일 서비스 및 Azure VM 에이전트에 대한 이진 파일을 가져올 위치 및 암호, 그리고 출력을 저장할 위치를 구성에 지정합니다.
- ‘xRemoteFile’을 사용하여 저장소에서 파일을 다운로드할 수 있도록 xPSDesiredStateConfiguration DscResource를 가져옵니다.
- 이진 파일을 저장하려는 디렉터리를 만듭니다.
- 보관 리소스를 통해 zip 폴더에서 파일을 추출합니다.
- 패키지 ‘Install’ 리소스는 특정 인수를 사용하여 UNIFIEDAGENT.EXE 설치 관리자에서 모바일 서비스를 설치합니다(인수를 생성하는 변수는 환경을 반영하도록 변경해야 함).
- 패키지 ‘AzureAgent’는 권장되는 Azure VM 에이전트를 Azure에서 실행하는 모든 VM에 설치하며, 또한 확장 파일을 VM 사후 장애 조치에 추가할 수 있도록 합니다.
- 서비스 리소스는 관련 모바일 서비스 및 Azure 서비스가 항상 실행하도록 합니다.

필자는 구성을 내 컴퓨터의 폴더에 **ASRMobilityService**로 저장했습니다.

(에이전트가 역시 올바른 암호를 사용하여 올바르게 연결되도록 구성의 CSIP를 실제 관리 서버가 반영되도록 바꿉니다.)

## 3단계 – OMS 자동화 DSC에 업로드

우리가 만든 DSC 구성은 필요한 DSC 리소스 모듈(xPSDesiredStateConfiguration)을 가져오므로 DSC 구성을 업로드하기 전에 OMS 자동화에서 해당 모듈을 가져와야 합니다.

자동화 계정에 로그인하고 AssetsàModules로 이동한 다음 갤러리 찾아보기를 클릭합니다.

여기서 모듈을 검색하고 계정으로 가져올 수 있습니다.

![모듈 가져오기](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

이 작업이 완료되면 Azure RM 모듈이 설치된 컴퓨터로 이동하여 새로 만든 DSC 구성 가져오기를 계속 진행합니다.

### Cmdlet 가져오기

PowerShell에서 Azure 구독에 로그온하고 cmdlet을 사용자 환경이 반영되도록 수정합니다.

먼저 필자는 다음 cmdlet을 사용하여 구성을 OMS 자동화 DSC에 업로드하려고 합니다.

```
Import-AzureRmAutomationDscConfiguration `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
                                        -Published `
                                        -Description "DSC Config for Mobility Service"
```

다음으로, 노드를 구성에 등록하기 시작할 수 있도록 OMS 자동화 DSC의 구성을 컴파일해야 합니다.

### OMS 자동화 DSC의 구성 컴파일

우리는 다음 cmdlet을 실행하여 이 목적을 달성합니다.

```
Start-AzureRmAutomationDscCompilationJob `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -ConfigurationName ASRMobilityService
```

우리는 기본적으로 구성을 호스트된 DSC 풀 서비스에 배포하고 있으므로 이 작업에 몇 분 정도 걸릴 수 있습니다.

이 작업이 완료되면 PowerShell(Get-AzureRmAutomationDscCompilationJob)을 사용하여 작업 정보를 검색하거나 portal.azure.com을 사용할 수 있습니다.

![작업 검색](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

이제 DSC 구성을 OMS 자동화 DSC에 게시하고 업로드했습니다.

## 4단계 – OMS 자동화 DSC에 컴퓨터 탑재
*이 시나리오를 완료하기 위한 사전 요구 사항 중 하나는 Windows 컴퓨터를 WMF의 최신 버전으로 업데이트하는 것입니다. 이 URL을 따라 플랫폼에 맞는 버전을 다운로드하여 설치할 수 있습니다. https://www.microsoft.com/download/details.aspx?id=50395*

이제 우리의 노드에 적용할 DSC에 대한 metaconfig를 만듭니다. 이 작업에 성공하려면 Azure에서 선택한 자동화 계정에 대한 끝점 URL과 기본 키를 검색해야 합니다.

이러한 값은 자동화 계정 ‘모든 설정' 블레이드에서 ‘Keys’ 아래에 있을 수 있습니다.

![키 값](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

필자의 환경에는 OMS Site Recovery로 보호하려는 Windows Server 2012 R2 물리적 서버가 있습니다.

레지스트리에 보류 중인 파일 이름 바꾸기 작업이 남아 있으면 보류 중인 다시 부팅 때문에 설치를 완료할 수 없으므로, 서버를 자동화 DSC 끝점에 연결하기 시작하기 전에 그러한 작업이 있는지 확인하는 것이 좋습니다.

### 레지스트리에서 보류 중인 파일 이름 바꾸기 작업 확인

다음 cmdlet을 실행하여 서버에 보류 중인 다시 부팅이 없는지 확인:

```
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations
```
레지스트리가 비어 있으면 계속 진행해도 좋습니다. 그렇지 않은 경우 유지 관리 기간 동안 서버를 다시 부팅하여 이 상황을 해소해야 합니다.

구성을 서버에 적용하기 위해 필자는 PowerShell ISE를 시작하고 다음 스크립트를 실행합니다. 이 스크립트는 기본적으로 로컬 구성 관리자 엔진에 대해 OMS 자동화 DSC에 등록하고 특정 구성(ASRMobilityService.localhost)을 검색하도록 지시하는 DSC 로컬 구성입니다.

```
[DSCLocalConfigurationManager()]

Configuration metaconfig
{
    node localhost
    {

        Settings
        {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
                ResourceRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }
                ConfigurationRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
                ConfigurationNames = 'ASRMobilityService.localhost'
            }
                ReportServerWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }

    }
}
metaconfig

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

이 구성은 로컬 구성 관리자를 OMS 자동화 DSC에 자신을 등록하도록 구성하며, 기본적으로 엔진이 작동하는 방법, 구성 편차가 있는 경우 수행해야 할 작업(ApplyAndAutoCorrect)을 지시하며, 필요한 다시 부팅이 있는 경우 그 후에 구성을 계속 진행합니다.

이 작업을 실행하면 노드는 자동화 DSC에 등록하기 시작해야 합니다.

![노드 등록](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

portal.azure.com으로 돌아가면 새로 등록한 노드가 이제 포털에 나타난 것을 확인할 수 있습니다.

![노드 등록](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

서버에서 다음 PowerShell cmdlet을 실행하여 올바르게 등록되었는지 확인합니다.

```
Get-DscLocalConfigurationManager
```

구성을 가져와 서버에 적용한 후 다음을 실행하여 이를 확인할 수 있습니다.

```
Get-DscConfigurationStatus
```

출력에 서버가 해당 구성을 가져온 것으로 나타납니다.

![노드 등록](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

또한 모바일 서비스 설정에는 ‘<SystemDrive>\\ProgramData\\ASRSetupLogs’에서 확인할 수 있는 자체의 로그가 있습니다.

이것으로 끝입니다. – 우리는 이제 Site Recovery를 사용하여 보호하려는 우리의 컴퓨터에 모바일 서비스를 배포하고 등록했으며 DSC를 기반으로 필요한 서비스가 언제나 실행되도록 할 수 있습니다.

![노드 등록](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

이와 같은 상황이 관리 서버에서 감지되면 계속해서 Site Recovery를 사용하여 보호를 구성하고 복제를 활성화할 수 있습니다.

## 연결이 끊어진 환경에서 DSC 사용

컴퓨터가 인터넷에 연결되어 있지 않은 경우에도 여전히 DSC를 기반으로 보호하려는 워크로드에 모바일 서비스를 배포하고 구성할 수 있습니다.

기본적으로 클라이언트가 DSC 끝점에 등록된 후 구성을 가져올 위치인 OMS 자동화 DSC에서 얻는 것과 같은 기능을 제공하는 환경에서 DSC 풀 서버를 인스턴스화할 수 있습니다. 그러나 푸시를 사용하는 또 다른 옵션이 있으며, 이 경우 로컬 컴퓨터에서 또는 원격으로 DSC 구성을 자신의 컴퓨터에 수동으로 푸시합니다.

참고로 이 예제에서는 컴퓨터 이름에 대한 매개 변수를 추가했고 원격 파일은 이제 보호하려는 컴퓨터에서 액세스할 수 있어야 하는 원격 공유 위치에 있으며, 스크립트의 끝에서 구성을 적용한 다음 DSC 구성을 대상 컴퓨터에 적용하기 시작합니다.

### 전제 조건

· xPSDesiredStateConfiguration PowerShell 모듈 설치

WMF 5.0이 설치된 Windows 컴퓨터의 경우 대상 컴퓨터에서 다음 cmdlet을 실행하기만 하면 xPSDesiredStateConfiguration 모듈을 설치할 수 있습니다.

```
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

WMF 4.0을 사용하여 모듈을 Windows 컴퓨터에 배포해야 하는 경우 PowerShellGet(WMF 5.0)이 있는 컴퓨터에서 이 cmdlet을 실행하여 모듈을 다운로드하고 저장할 수도 있습니다.

```
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

또한 WMF 4.0의 경우 다음 업데이트가 컴퓨터에 설치되었는지 확인합니다.

https://www.microsoft.com/download/details.aspx?id=40749

WMF 5.0 및 4.0을 사용하는 Windows 컴퓨터에 모두 다음 구성을 푸시할 수 있습니다.

```
configuration ASRMobilityService {

    param (
    [Parameter(Mandatory=$true)]
    $computername
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $computername {      

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
ASRMobilityService

Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose
```

회사 네트워크 내에서 자신의 DSC 풀 서버를 인스턴스화하려는 경우 OMS 자동화 DSC에서 가져올 수 있는 것과 같은 기능을 모방하려면 다음 가이드를 참조합니다. https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## 선택 사항: Azure Resource Manager 템플릿을 사용하여 DSC 구성 배포

이 문서에서는 지금까지 모바일 서비스 및 Azure VM 에이전트를 자동으로 배포하고 이들이 보호하려는 컴퓨터에서 확실히 실행하도록 하는 자신의 DSC 구성을 만들 수 있는 방법에 초점을 맞추었습니다. 추가 이점으로 이 DSC 구성을 새 또는 기존 Azure 자동화 계정에 배포하여 템플릿의 입력 매개 변수를 통해 환경에 맞는 변수를 포함하고 있는 자동화 자산을 만드는 Azure Resource Manager#### 템플릿도 갖게 되었습니다.

배포된 후 이 가이드의 4단계를 참조하여 컴퓨터를 탑재할 수 있습니다.

템플릿은 다음 작업을 수행합니다.

· 기존 OMS 자동화 계정 사용 또는 새로 만들기

· 자신에게 맞는 입력 매개 변수 선택:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


위의 모든 단계를 올바른 순서대로 가져오므로 보호를 위해 컴퓨터를 탑재하여 쉽게 다시 시작할 수 있습니다.

배포 지침이 포함된 템플릿은 다음 위치에 있음:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

PowerShell을 사용하여 배포

```
$OMSAutomationRGname = 'KNOMS'

$DSCJobGuid = (New-Guid).Guid

New-AzureRmResourceGroupDeployment `
                                  -Name "DSC3" `
                                  -ResourceGroupName $OMSAutomationRGname `
                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
                                  -OMSAutomationAccountName KNOMSAA `
                                  -ASRRemoteFile "https://knrecstor01.blob.core.windows.net/asr/ASR.zip" `
                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.windows.net/asr/passphrase.txt" `
                                  -ASRCSEndpoint '10.0.0.115' `
                                  -DSCJobGuid $DSCJobGuid `
                                  -Verbose
```

## 다음 단계:

모바일 서비스 에이전트를 배포한 후 가상 컴퓨터에 대해 [복제본 활성화](site-recovery-vmware-to-azure.md#step-6-replicate-applications)를 계속할 수 있습니다.

<!---HONumber=AcomDC_0810_2016-->