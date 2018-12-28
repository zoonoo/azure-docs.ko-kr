---
title: Azure에서 서비스 맵 구성 | Microsoft Docs
description: 서비스 맵은 Windows 및 Linux 시스템의 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 사용자 환경에 서비스 맵을 배포하고 다양한 시나리오에서 사용하는 것에 대해 자세히 설명합니다.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: ee0de5d03de29adddd8f77efbe7491603cc0e4c4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188795"
---
# <a name="configure-service-map-in-azure"></a>Azure에서 서비스 맵 구성
서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 이것을 사용하여 서버를 생각한 것처럼(중요한 서비스를 제공하는 상호 연결된 시스템으로) 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다.

이 문서에서는 서비스 맵 및 에이전트 등록에 대해 자세히 설명합니다. 서비스 맵 사용에 대한 자세한 내용은 [Azure에서 서비스 맵 솔루션 사용]( service-map.md)을 참조하세요.

## <a name="supported-azure-regions"></a>지원되는 Azure 지역
서비스 맵은 현재 다음과 같은 Azure 지역에서 사용할 수 있습니다.
- 미국 동부
- 서유럽
- 미국 중서부
- 동남아시아

## <a name="supported-windows-operating-systems"></a>지원되는 Windows 운영 체제
다음 섹션에서는 Windows에서 종속성 에이전트에 대해 지원되는 운영 체제를 나열합니다. 

>[!NOTE]
>서비스 맵은 64비트 플랫폼만 지원합니다.
>

### <a name="windows-server"></a>Windows Server
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows 데스크톱
- Windows 10 1803
- 윈도우 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제
다음 섹션에서는 Red Hat Enterprise Linux, CentOS Linux 및 Oracle Linux(RHEL 커널 포함)에서 종속성 에이전트에 대해 지원되는 운영 체제를 나열합니다.  

- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 "2.6.16.21-0.8-xen"의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.
- CentOSPlus 커널은 지원되지 않습니다.
- Oracle UEK(Unbreakable Enterprise Kernel)에 대해서는 이 문서의 뒷부분에서 다룹니다.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 버전 | 커널 버전 |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel을 갖춘 Oracle Enterprise Linux 6
| OS 버전 | 커널 버전
|:--|:--|
| 6.2 | Oracle 2.6.32-300(UEK R1) |
| 6.3 | Oracle 2.6.39-200(UEK R2) |
| 6.4 | Oracle 2.6.39-400(UEK R2) |
| 6.5 | Oracle 2.6.39-400(UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400(UEK R2 i386) |

### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel을 갖춘 Oracle Enterprise Linux 5

| OS 버전 | 커널 버전
|:--|:--|
| 5.10 | Oracle 2.6.39-400(UEK R2) |
| 5.11 | Oracle 2.6.39-400(UEK R2) |

## <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

## <a name="dependency-agent-downloads"></a>종속성 에이전트 다운로드

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>연결된 소스
서비스 맵은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Dependency Agent는 Log Analytics 연결을 위한 Log Analytics 에이전트에 따라 달라집니다. 즉, 서버에 먼저 Log Analytics 에이전트를 설치하고 Dependency Agent로 구성해야 합니다.  다음 표는 서비스 맵 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
|:--|:--|:--|
| Windows 에이전트 | yes | 서비스 맵은 Windows 컴퓨터에서 데이터를 분석하고 수집합니다. <br><br>[Windows용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 외에도 Windows 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| Linux 에이전트 | yes | 서비스 맵은 Linux 컴퓨터에서 데이터를 분석하고 수집합니다. <br><br>[Linux용 Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md) 외에도 Linux 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](#supported-operating-systems)를 참조하세요. |
| System Center Operations Manager 관리 그룹 | yes | 서비스 맵은 연결된 [System Center Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md)의 Windows 및 Linux 에이전트에서 데이터를 분석하고 수집합니다. <br><br>System Center Operations Manager 에이전트 컴퓨터에서 Log Analytics로의 직접 연결이 필요합니다. |
| Azure Storage 계정 | 아니요 | 서비스 맵은 에이전트 컴퓨터에서 데이터를 수집하므로 Azure Storage에서 수집할 데이터는 없습니다. |

Windows에서 System Center Operations Manager와 Log Analytics는 MMA(Microsoft Monitoring Agent)를 사용하여 모니터링 데이터를 수집하고 전송합니다. (이 에이전트는 컨텍스트에 따라 System Center Operations Manager 에이전트, Log Analytics 에이전트, MMA 또는 직접 에이전트라고 합니다.) System Center Operations Manager와 Log Analytics는 MMA의 다양한 기본 버전을 제공합니다. 이러한 버전은 각각 System Center Operations Manager, Log Analytics 또는 양쪽 모두에 보고할 수 있습니다.  

Linux에서는 Linux용 Log Analytics 에이전트가 모니터링 데이터를 수집하여 Log Analytics에 보냅니다. 서비스에 직접 연결되거나 Log Analytics와 통합된 Operations Manager 관리 그룹에 보고하는 Log Analytics 에이전트를 통해 서버에 있는 서비스 맵을 사용할 수 있습니다.  

이 문서에서는 System Center Operations Manager 관리 그룹에 연결되어 있든 또는 Log Analytics에 직접 연결되어 있든 관계없이 Linux 또는 Windows의 모든 에이전트를 *Log Analytics 에이전트*라고 하겠습니다. 

서비스 맵 에이전트는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. 서비스 맵의 데이터는 항상 Log Analytics 에이전트에 의해 직접 또는 Log Analytics 게이트웨이를 통해 Log Analytics로 전송됩니다.

![서비스 맵 에이전트](media/service-map-configure/agents.png)

Log Analytics에 연결된 관리 그룹을 사용하는 System Center Operations Manager 고객인 경우:

- System Center Operations Manager 에이전트가 인터넷에 액세스하여 Log Analytics에 연결할 수 있으면 추가 구성이 필요하지 않습니다.  
- System Center Operations Manager 에이전트가 인터넷을 통해 Log Analytics에 액세스할 수 없는 경우 Log Analytics 게이트웨이를 System Center Operations Manager와 작동하도록 구성해야 합니다.
  
Windows 또는 Linux 컴퓨터를 서비스에 직접 연결할 수 없으면, 게이트웨이를 사용하여 Log Analytics 작업 영역에 연결하도록 Log Analytics 에이전트를 구성해야 합니다. Log Analytics 게이트웨이를 배포하고 구성하는 방법에 자세한 내용은 [Log Analytics 게이트웨이를 사용하여 인터넷 액세스 없이 컴퓨터 연결](../../azure-monitor/platform/gateway.md)을 참조하세요.  

### <a name="management-packs"></a>관리 팩
Log Analytics 작업 영역에서 서비스 맵이 활성화되면 해당 작업 영역의 모든 Windows 서버에 300KB 관리 팩이 전송됩니다. [연결된 관리 그룹](../../azure-monitor/platform/om-agents.md)에서 System Center Operations Manager 에이전트를 사용하는 경우 서비스 맵 관리 팩은 System Center Operations Manager에서 배포됩니다. 

관리 팩 이름은 Microsoft.IntelligencePacks.ApplicationDependencyMonitor입니다. 이것은 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\에 기록됩니다. 관리 팩에 사용된 데이터 원본은 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll입니다.

## <a name="data-collection"></a>데이터 수집
각 에이전트는 시스템 종속성이 얼마나 복잡한지에 따라 일일 약 25MB를 전송할 것으로 기대할 수 있습니다. 각 에이전트는 15초마다 서비스 맵 종속성 데이터를 보냅니다.  

종속성 에이전트는 일반적으로 0.1%의 시스템 메모리와 0.1%의 시스템 CPU를 사용합니다.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터
Microsoft는 서비스 맵 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스 맵 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 데이터에는 운영 체제 및 버전과 같은 소프트웨어 구성 정보가 포함됩니다. 정확하고 효율적인 문제 해결 기능을 제공하기 위해 IP 주소, DNS 이름 및 워크스테이션 이름도 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

## <a name="installation"></a>설치

### <a name="azure-vm-extension"></a>Azure VM 확장
Windows(DependencyAgentWindows)와 Linux(DependencyAgentLinux) 모두에 사용할 수 있는 확장이 있으며, [Azure VM 확장](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features)을 사용하여 Azure VM에 Dependency Agent를 쉽게 배포할 수 있습니다.  Azure VM 확장을 사용하면 PowerShell 스크립트를 사용하거나 VM의 Azure Resource Manager 템플릿을 사용하여 직접 Dependency Agent를 Windows 및 Linux VM에 배포할 수 있습니다.  Azure VM 확장을 통해 에이전트를 배포하는 경우 에이전트가 자동으로 최신 버전으로 업데이트됩니다.

PowerShell을 통해 Azure VM 확장을 배포하려면 다음 예제를 사용할 수 있습니다.

```PowerShell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

VM에 Dependency Agent가 설치되어 있는지 확인하는 것보다 더 쉬운 방법은 Azure Resource Manager 템플릿에 에이전트를 포함시키는 것입니다.  다음 JSON 코드 예제를 템플릿의 *resources* 섹션에 추가할 수 있습니다.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Microsoft Windows에 Dependency Agent 설치
`InstallDependencyAgent-Windows.exe`를 실행하면 Dependency Agent를 수동으로 Windows 컴퓨터에 설치할 수 있습니다. 옵션 없이 이 실행 파일을 실행하면 설치 마법사가 시작되고 대화형으로 설치를 수행할 수 있습니다.  

>[!NOTE]
>에이전트를 설치 또는 제거하려면 관리자 권한이 필요합니다.

각 Windows 컴퓨터에서 Dependency Agent를 설치하려면 다음 단계를 사용합니다.

1.  [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)에 설명된 방법 중 하나를 수행하여 Windows용 Log Analytics 에이전트를 설치합니다.
2.  Windows 에이전트를 다운로드하고 다음 명령을 사용하여 실행합니다. 
    
    `InstallDependencyAgent-Windows.exe`

3.  설치 마법사에 따라 에이전트를 설치합니다.
4.  Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Windows 에이전트에서 로그 디렉터리는 %Programfiles%\Microsoft Dependency Agent\logs입니다. 

#### <a name="windows-command-line"></a>Windows 명령줄
다음 표의 옵션을 사용하여 명령줄에서 설치합니다. 설치 플래그 목록을 보려면 /?를 사용하여 설치 관리자를 실행하십시오. 플래그를 사용하여 설치 관리자를 실행합니다.

    InstallDependencyAgent-Windows.exe /?

| 플래그 | 설명 |
|:--|:--|
| /? | 명령줄 옵션 목록을 가져옵니다. |
| /S | 사용자 프롬프트 없이 자동 설치를 수행합니다. |

Windows Dependency Agent에 대한 파일은 기본적으로 C:\Program Files\Microsoft Dependency Agent에 있습니다.

### <a name="install-the-dependency-agent-on-linux"></a>Linux에 Dependency Agent 설치
Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 `InstallDependencyAgent-Linux64.bin`을 사용하여 Linux 컴퓨터에 설치됩니다. `sh`를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

>[!NOTE]
> 에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.

각 Linux 컴퓨터에서 Dependency Agent를 설치하려면 다음 단계를 사용합니다.

1.  [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)에 설명된 방법 중 하나를 수행하여 Log Analytics 에이전트를 설치합니다.
2.  다음 명령을 사용하여 루트로 Linux Dependency Agent를 설치합니다.
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 /var/opt/microsoft/dependency-agent/log입니다.

설치 플래그 목록을 보려면 다음과 같이 -help 플래그로 설치 프로그램을 실행합니다.

    InstallDependencyAgent-Linux64.bin -help

| 플래그 | 설명 |
|:--|:--|
| -help | 명령줄 옵션 목록을 가져옵니다. |
| -s | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

Dependency Agent에 대한 파일은 다음 디렉터리에 있습니다.

| 파일 | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 저장소 파일 | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>설치 스크립트 예제
Dependency Agent를 한 번에 여러 서버로 손쉽게 배포하려면 다음 스크립트 예제를 다운로드하여 Windows 또는 Linux에서 Dependency Agent를 설치합니다.

### <a name="powershell-script-for-windows"></a>Windows용 PowerShell 스크립트
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Linux용 셸 스크립트
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```
## <a name="desired-state-configuration"></a>필요한 상태 구성
DSC(필요한 상태 구성)를 통해 Dependency Agent를 배포하려면 xPSDesiredStateConfiguration 모듈을 다음과 같은 코드와 함께 사용할 수 있습니다.

```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="remove-the-dependency-agent"></a>Dependency Agent 제거
### <a name="uinstall-agent-on-windows"></a>Windows에서 에이전트 제거
관리자는 제어판을 통해 Windows용 Dependency Agent를 제거할 수 있습니다.

관리자는 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe를 실행하여 Dependency Agent를 제거할 수도 있습니다.

### <a name="uninstall-agent-on-linux"></a>Linux에서 에이전트 제거
다음 명령을 사용하여 Linux에서 Dependency Agent를 제거할 수 있습니다.

RHEL, CentOs 또는 Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>문제 해결
서비스 맵을 설치하거나 실행하는 데 문제가 있으면 이 섹션이 도움이 될 수 있습니다. 그래도 문제를 해결할 수 없으면 Microsoft 지원에 문의해 주세요.

### <a name="dependency-agent-installation-problems"></a>Dependency Agent 설치 문제
#### <a name="installer-prompts-for-a-reboot"></a>재부팅용 설치 관리자 프롬프트
Dependency Agent는 *일반적으로* 설치 또는 제거 시 재부팅하지 않아도 됩니다. 그러나 드문 경우이지만 설치를 계속하기 위해 Windows Server를 다시 부팅해야 합니다. 이것은 종속성(일반적으로 Microsoft Visual C ++ 재배포 가능 패키지)이 잠긴 파일로 인해 재부팅해야 하는 경우 발생합니다.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>"종속성 에이전트를 설치할 수 없습니다. Visual Studio 런타임 라이브러리를 설치하지 못했습니다(코드 = [코드 번호])" 메시지가 표시됩니다.

Microsoft Dependency Agent는 Microsoft Visual Studio 런타임 라이브러리를 기반으로 합니다. 라이브러리를 설치하는 동안 문제가 발생하면 메시지가 표시됩니다. 

런타임 라이브러리 설치 관리자는 %LOCALAPPDATA%\temp 폴더에 로그를 만듭니다. 파일은 dd_vcredist_arch_yyyymmddhhmmss.log이며, 여기서 *arch*는 "x86" 또는 "amd64"이고 *yyyymmddhhmmss*는 로그를 만든 날짜 및 시간(24시간제)입니다. 로그는 설치를 방해하는 문제에 대한 세부 내용을 제공합니다.

먼저 [최신 런타임 라이브러리](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)를 직접 설치하는 것이 유용할 수 있습니다.

다음 테이블에는 코드 번호 및 제안된 해결 방법이 나와 있습니다.

| 코드 | 설명 | 해결 방법 |
|:--|:--|:--|
| 0x17 | 라이브러리 설치 관리자에는 설치하지 않은 Windows 업데이트가 필요합니다. | 가장 최근의 라이브러리 설치 관리자 로그를 확인합니다.<br><br>"Windows8.1-KB2999226-x64.msu" 참조 뒤에 "오류 0x80240017: MSU 패키지를 실행하지 못했습니다"라는 줄이 나오면 KB2999226을 설치하기 위한 필수 구성 요소가 없는 것입니다. [Windows의 유니버설 C 런타임](https://support.microsoft.com/kb/2999226) 필수 구성 요소 섹션의 지침을 따릅니다. 필수 구성 요소를 설치하려면 Windows Update를 실행하고 여러 번 다시 부팅해야 할 수 있습니다.<br><br>Microsoft 종속성 에이전트 설치 관리자를 다시 실행합니다. |

### <a name="post-installation-issues"></a>사후 설치 문제
#### <a name="server-doesnt-appear-in-service-map"></a>서버가 서비스 맵에 표시되지 않습니다.
종속성 에이전트 설치에 성공했지만 서비스 맵 솔루션에 서버가 표시되지 않는 경우:
* 종속성 에이전트가 성공적으로 설치되었나요? 서비스가 설치되어 실행 중인지 확인하여 성공적인 설치 여부를 검사할 수 있습니다.<br><br>
**Windows**: "Microsoft 종속성 에이전트"라는 서비스를 찾아봅니다.<br>
**Linux**: "microsoft-dependency-agent" 실행 프로세스를 찾아봅니다.

* [Operations Management Suite/Log Analytics의 무료 가격 책정 등급](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)에 있습니까? 무료 계획에서는 고유한 서비스 맵 서버를 5개까지 허용합니다. 따라서 이전의 5개 서비스 맵 서버에서 데이터를 더 이상 보내지 않더라도 이후의 모든 서버는 서비스 맵에 표시되지 않습니다.

* 서버에서 Log Analytics로 로그 및 perf 데이터를 보내고 있나요? [로그 검색]으로 이동하여 컴퓨터에 대해 다음 쿼리를 실행합니다. 

        Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType

결과에서 다양한 이벤트를 얻었나요? 최근 데이터인가요? 그렇다면 Log Analytics 에이전트가 올바르게 작동하고 Log Analytics와 통신하고 있습니다. 그렇지 않으면 [Windows용 Log Analytics 에이전트 문제 해결](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) 또는 [Linux용 Log Analytics 에이전트 문제 해결](../../azure-monitor/platform/agent-linux-troubleshoot.md)을 참조하여 서버의 에이전트를 확인합니다.

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>서버가 서비스 맵에 표시되지만 프로세스가 없습니다.
서비스 맵에서 서버가 표시되지만 프로세스 또는 연결 데이터가 없는 경우 종속 에이전트가 설치되어 실행 중이지만 커널 드라이버가 로드되지 않았음을 나타냅니다. 

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log 파일(Windows) 또는 /var/opt/microsoft/dependency-agent/log/service.log 파일(Linux)을 확인합니다. 파일의 마지막 줄에 커널이 로드되지 않은 이유가 표시되어야 합니다. 예를 들어, 커널을 업데이트 한 경우 Linux에서 커널이 지원되지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 서비스 맵이 배포 및 구성된 후 [서비스 맵을 사용]( service-map.md)하는 방법을 알아봅니다.
