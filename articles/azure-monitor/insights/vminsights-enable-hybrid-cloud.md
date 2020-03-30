---
title: 하이브리드 환경에 대한 Azure 모니터 사용 | 마이크로 소프트 문서
description: 이 문서에서는 하나 이상의 가상 컴퓨터가 포함된 하이브리드 클라우드 환경에 대해 VM에 대한 Azure Monitor를 사용하도록 설정하는 방법에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480745"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>하이브리드 환경에 대한 VM용 Azure 모니터 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 데이터 센터 또는 기타 클라우드 환경에서 호스팅되는 가상 컴퓨터 또는 물리적 컴퓨터에 대해 VM용 Azure Monitor를 사용하도록 설정하는 방법을 설명합니다. 이 프로세스가 끝나면 사용자 환경에서 가상 컴퓨터를 모니터링하기 성공적으로 시작하고 성능 또는 가용성 문제가 발생하는지 알아봅니다.

시작하려면 먼저 [사전 요구 사항](vminsights-enable-overview.md)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. [Log Analytics Linux 및 Windows 에이전트](../../log-analytics/log-analytics-agent-overview.md)에 대한 요구 사항 및 배포 방법을 검토합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>VM용 Azure Monitor 맵 Dependency Agent는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. IT 보안 정책에서 네트워크의 컴퓨터가 인터넷에 연결할 수 없는 경우 지도 데이터는 항상 Log Analytics 에이전트가 직접 또는 [운영 관리 제품군 게이트웨이를](../../azure-monitor/platform/gateway.md) 통해 Azure Monitor 서비스로 전송됩니다.

이 작업을 완료하는 단계는 다음과 같이 요약됩니다.

1. 윈도우 또는 리눅스에 대 한 로그 분석 에이전트를 설치 합니다. 에이전트를 설치하기 전에 [Log Analytics 에이전트 개요](../platform/log-analytics-agent.md) 문서를 검토하여 시스템 전제 조건 및 배포 방법을 이해합니다.

2. [Windows](https://aka.ms/dependencyagentwindows) 또는 [Linux](https://aka.ms/dependencyagentlinux)에 대한 VM용 Azure Monitor 맵 Dependency Agent를 다운로드 및 설치합니다.

3. 성능 카운터 컬렉션을 사용하도록 설정합니다.

4. VM용 Azure Monitor를 배포합니다.

>[!NOTE]
>종속성 에이전트를 배포하기 위해 이 문서에서 설명하는 정보는 [서비스 맵 솔루션에도](service-map.md)적용됩니다.  

## <a name="install-the-dependency-agent-on-windows"></a>Windows에 종속성 에이전트 설치

`InstallDependencyAgent-Windows.exe`를 실행하면 Dependency Agent를 수동으로 Windows 컴퓨터에 설치할 수 있습니다. 옵션 없이 이 실행 파일을 실행하면 설치 마법사가 시작되고 에이전트를 대화형으로 설치할 수 있습니다.

>[!NOTE]
>에이전트를 설치 또는 제거하려면 *관리자* 권한이 필요합니다.

다음 표에는 명령줄에서 에이전트 설정을 통해 지원되는 매개 변수가 강조 표시되어 있습니다.

| 매개 변수 | 설명 |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 `/?` 매개 변수를 사용하여 설치 프로그램을 실행하려면 **설치의존도에이전트-Windows.exe/?**

Windows Dependency Agent에 대한 파일은 기본적으로 *C:\Program Files\Microsoft Dependency Agent*에 설치됩니다. 설치가 완료된 후 종속성 에이전트가 시작되지 않으면 로그에서 자세한 오류 정보를 확인합니다. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs*입니다.

## <a name="install-the-dependency-agent-on-linux"></a>Linux에 Dependency Agent 설치

Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 *InstallDependencyAgent-Linux64.bin*을 사용하여 Linux 서버에 설치됩니다. `sh`를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

>[!NOTE]
> 에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.
>

| 매개 변수 | 설명 |
|:--|:--|
| -help | 명령줄 옵션 목록을 가져옵니다. |
| -S | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

예를 들어 `-help` 매개 변수를 사용하여 설치 프로그램을 실행하려면 **설치의존에이전트-Linux64.bin -help**를 입력합니다.

명령을 `sh InstallDependencyAgent-Linux64.bin`실행 하여 Linux 종속성 에이전트를 루트로 설치합니다.

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리 */var/opt/microsoft/종속성 에이전트/로그입니다.*

Dependency Agent에 대한 파일은 다음 디렉터리에 있습니다.

| 파일 | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 스토리지 파일 | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>설치 스크립트 예제

Dependency Agent를 한 번에 여러 서버로 손쉽게 배포하려면 다음 스크립트 예제를 다운로드하여 Windows 또는 Linux에서 Dependency Agent를 설치합니다.

### <a name="powershell-script-for-windows"></a>Windows용 PowerShell 스크립트

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Linux용 셸 스크립트

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

DSC(필요한 상태 구성)를 통해 Dependency Agent를 배포하려면 xPSDesiredStateConfiguration 모듈을 다음과 같은 코드와 함께 사용할 수 있습니다.

```powershell
configuration VMInsights {

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

## <a name="enable-performance-counters"></a>성능 카운터 사용하도록 설정

솔루션에서 참조되는 Log Analytics 작업 영역이 솔루션에 필요한 성능 카운터를 수집하도록 구성되지 않은 경우 사용하도록 설정해야 합니다. 다음 두 가지 방법 중 하나로 수행할 수 있습니다.
* 수동으로([Log Analytics의 Windows 및 Linux 성능 데이터 원본](../../azure-monitor/platform/data-sources-performance-counters.md)에 설명되어 있음)
* [Azure PowerShell 갤러리에서](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) 사용할 수 있는 PowerShell 스크립트를 다운로드하고 실행하여

## <a name="deploy-azure-monitor-for-vms"></a>VM용 Azure Monitor를 배포합니다.

이 방법은 Log Analytics 작업 영역에 솔루션 구성 요소를 사용하도록 구성을 지정하는 JSON 템플릿을 포함합니다.

템플릿을 사용하여 리소스를 배포하는 방법을 모르는 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하려면 먼저 CLI를 로컬로 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드하려면 [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli)참조하십시오.

### <a name="create-and-execute-a-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 이 파일을 *installsolutionsforvminsights.json*으로 로컬 폴더에 저장합니다.

1. *WorkspaceName*, *ResourceGroupName* 및 *WorkspaceLocation*의 값을 캡처합니다. *WorkspaceName* 값은 Log Analytics 작업 영역의 이름입니다. *WorkspaceLocation* 값은 작업 영역이 정의된 지역입니다.

1. 다음 PowerShell 명령을 사용하여 이 템플릿을 배포할 준비가 되었습니다.

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    구성 변경을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 작업이 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    provisioningState       : Succeeded
    ```
   모니터링을 사용하도록 설정하고 약 10분 후에 하이브리드 컴퓨터에 대한 상태 및 메트릭을 볼 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="vm-doesnt-appear-on-the-map"></a>VM이 맵에 나타나지 않습니다.

종속성 에이전트 설치가 성공했지만 맵에 컴퓨터가 표시되지 않으면 다음 단계를 수행하여 문제를 진단합니다.

1. 종속성 에이전트가 성공적으로 설치되었나요? 서비스가 설치되어 실행 중인지 확인하여 성공적인 설치 여부를 검사할 수 있습니다.

    **Windows**: “Microsoft 종속성 에이전트”라는 서비스를 찾아봅니다.

    **Linux**: "microsoft-dependency-agent" 실행 프로세스를 찾아봅니다.

2. [로그 분석의 무료 가격 책정 계층에](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)있습니까? 무료 요금제는 최대 5대의 고유 컴퓨터를 허용합니다. 이전 5대의 컴퓨터가 더 이상 데이터를 보내지 않더라도 후속 컴퓨터는 맵에 표시되지 않습니다.

3. 컴퓨터가 Azure 모니터 로그로 로그 및 퍼프 데이터를 전송하고 있습니까? 컴퓨터에 대해 다음 쿼리를 수행합니다.

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    하나 이상의 결과를 반환했습니까? 최근 데이터인가요? 이 경우 Log Analytics 에이전트가 올바르게 작동하고 서비스와 통신하고 있습니다. 그렇지 않으면 [Windows용 Log Analytics 에이전트 문제 해결](../platform/agent-windows-troubleshoot.md) 또는 [Linux용 Log Analytics 에이전트 문제 해결](../platform/agent-linux-troubleshoot.md)을 참조하여 서버의 에이전트를 확인합니다.

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>컴퓨터가 지도에 나타나지만 프로세스가 없습니다.

맵에 서버가 표시되지만 프로세스 또는 연결 데이터가 없는 경우 종속성 에이전트가 설치되고 실행중이지만 커널 드라이버가 로드되지 않았음을 나타냅니다.

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log 파일(Windows) 또는 /var/opt/microsoft/dependency-agent/log/service.log 파일(Linux)을 확인합니다. 파일의 마지막 줄에 커널이 로드되지 않은 이유가 표시되어야 합니다. 예를 들어, 커널을 업데이트 한 경우 Linux에서 커널이 지원되지 않을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제 가상 시스템에 대해 모니터링이 활성화되었으므로 이 정보는 VM용 Azure 모니터를 통해 분석할 수 있습니다.

- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.

- VM 성능으로 병목 현상 및 전반적인 사용률을 식별하려면 [Azure VM 성능 보기를](vminsights-performance.md)참조하십시오.
