---
title: 하이브리드 환경에 대 한 Azure Monitor (미리 보기)를 사용 하도록 설정 | Microsoft Docs
description: 이 문서는 하나 이상의 가상 컴퓨터가 포함 된 하이브리드 클라우드 환경에 대 한 Vm에 대 한 Azure Monitor을 사용 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122559"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>하이브리드 환경에 대 한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 가상 머신 또는 물리적 컴퓨터를 데이터 센터 또는 다른 클라우드 환경에서 호스트에 대 한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 하는 방법에 설명 합니다. 이 프로세스의 끝은 성공적으로 시작 환경의 가상 컴퓨터를 모니터링 하는 경우 이러한 문제가 발생 한 성능 또는 가용성에 알아봅니다. 

시작 하기 전에 검토 해야 합니다 [필수 구성 요소](vminsights-enable-overview.md) 구독 및 리소스 요구 사항을 충족 하는지 확인 합니다. [Log Analytics Linux 및 Windows 에이전트](../../log-analytics/log-analytics-agent-overview.md)에 대한 요구 사항 및 배포 방법을 검토합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>VM용 Azure Monitor 맵 Dependency Agent는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. 지도 데이터는 Log Analytics 에이전트를 통해 또는 직접 Azure Monitor 서비스에 의해 항상 전송 합니다 [Operations Management Suite 게이트웨이](../../azure-monitor/platform/gateway.md) IT 보안 정책이 네트워크의 컴퓨터를 허용 하지 않습니다 하는 경우 인터넷에 연결 합니다.

이 작업을 완료 하는 단계는 다음과 같이 요약 됩니다.

1. Windows 또는 Linux 용 Log Analytics 에이전트를 설치 합니다. 에이전트를 설치 하기 전에 검토 합니다 [Log Analytics 에이전트 개요](../platform/log-analytics-agent.md) 시스템 필수 구성 요소 및 배포 방법을 이해 하는 문서입니다.

2. [Windows](https://aka.ms/dependencyagentwindows) 또는 [Linux](https://aka.ms/dependencyagentlinux)에 대한 VM용 Azure Monitor 맵 Dependency Agent를 다운로드 및 설치합니다.

3. 성능 카운터 컬렉션을 사용하도록 설정합니다.

4. VM용 Azure Monitor를 배포합니다.

## <a name="install-the-dependency-agent-on-windows"></a>Windows에 종속성 에이전트 설치
`InstallDependencyAgent-Windows.exe`를 실행하면 Dependency Agent를 수동으로 Windows 컴퓨터에 설치할 수 있습니다. 옵션 없이 이 실행 파일을 실행하면 설치 마법사가 시작되고 에이전트를 대화형으로 설치할 수 있습니다.

>[!NOTE]
>에이전트를 설치 또는 제거하려면 *관리자* 권한이 필요합니다.

다음 표에는 명령줄에서 에이전트 설정을 통해 지원되는 매개 변수가 강조 표시되어 있습니다.

| 매개 변수 | 설명 |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 사용 하 여 설치 프로그램을 실행 하는 `/?` 매개 변수 입력 **Windows.exe /?** 합니다.

Windows Dependency Agent에 대한 파일은 기본적으로 *C:\Program Files\Microsoft Dependency Agent*에 설치됩니다. 종속성 에이전트 설치가 완료 된 후 시작 되지 않으면, 자세한 오류 정보에 대 한 로그를 확인 합니다. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs*입니다.

## <a name="install-the-dependency-agent-on-linux"></a>Linux에 Dependency Agent 설치
Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 *InstallDependencyAgent-Linux64.bin*을 사용하여 Linux 서버에 설치됩니다. `sh`를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

>[!NOTE]
> 에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.
>

| 매개 변수 | 설명 |
|:--|:--|
| -help | 명령줄 옵션 목록을 가져옵니다. |
| -s | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

예를 들어 사용 하 여 설치 프로그램을 실행 하는 `-help` 매개 변수 입력 **InstallDependencyAgent-Linux64.bin-도움말**합니다.

명령을 실행 하 여 루트로 Linux 종속성 에이전트를 설치 `sh InstallDependencyAgent-Linux64.bin`합니다.

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 */var/opt/microsoft/dependency-agent/log*입니다.

Dependency Agent에 대한 파일은 다음 디렉터리에 있습니다.

| 파일 | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 저장소 파일 | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>성능 카운터 사용하도록 설정
솔루션에서 참조되는 Log Analytics 작업 영역이 솔루션에 필요한 성능 카운터를 수집하도록 구성되지 않은 경우 사용하도록 설정해야 합니다. 두 가지 방법 중 하나로 수행할 수 있습니다.
* 수동으로([Log Analytics의 Windows 및 Linux 성능 데이터 원본](../../azure-monitor/platform/data-sources-performance-counters.md)에 설명되어 있음)
* 다운로드에서 사용할 수 있는 PowerShell 스크립트를 실행 하 여 [Azure PowerShell 갤러리](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>VM용 Azure Monitor를 배포합니다.
이 방법은 Log Analytics 작업 영역에 솔루션 구성 요소를 사용하도록 구성을 지정하는 JSON 템플릿을 포함합니다.

템플릿을 사용 하 여 리소스를 배포 하는 방법을 모 르 참조:
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용 하려면 먼저를 설치 하 여 로컬로 CLI를 사용 해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 를 설치 하거나 업그레이드 Azure CLI 참조 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
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

    구성 변경을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 완료 되 면 다음과 유사한 결과 포함 하는 다음 메시지가 나타납니다.

    ```powershell
    provisioningState       : Succeeded
    ```
   모니터링을 사용하도록 설정하고 약 10분 후에 하이브리드 컴퓨터에 대한 상태 및 메트릭을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 컴퓨터에 대 한 모니터링이 활성화 했으므로이 정보는 Vm에 대 한 Azure Monitor를 사용 하 여 분석을 위해 사용할 수 있습니다.
 
- 상태 기능을 사용 하는 방법에 알아보려면 참조 [Vm 상태에 대 한 Azure Monitor 뷰](vminsights-health.md)합니다.
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.
- 병목 현상 및 VM의 성능으로 전체적인 사용률을 확인 하려면 [보기 Azure VM의 성능](vminsights-performance.md)합니다.
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.