---
title: Edge Pro GPU 장치 Azure Stack에서 Azure Monitor 사용
description: Azure Stack Edge Pro GPU 장치에서 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c2f66895fccd14dcffd8c5570f1d5f46933090aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439190"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에서 Azure Monitor 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

여러 계산 응용 프로그램을 실행 하는 경우에는 Azure Stack Edge Pro GPU 장치에서 컨테이너를 모니터링 하는 것이 중요 합니다. Azure Monitor를 사용 하 여 장치에서 실행 되는 Kubernetes 클러스터의 컨테이너 로그와 메모리 및 프로세서 메트릭을 수집할 수 있습니다.

이 문서에서는 장치에서 Azure Monitor를 사용 하도록 설정 하 고 Log Analytics 작업 영역에서 컨테이너 로그를 수집 하는 데 필요한 단계를 설명 합니다. Azure Monitor 메트릭 저장소는 현재 Azure Stack Edge Pro GPU 장치에서 지원 되지 않습니다. 


## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음이 필요 합니다.

- Azure Stack Edge Pro 장치입니다. [자습서: 장치 활성화](azure-stack-edge-gpu-deploy-activate.md)의 단계에 따라 장치가 활성화 되었는지 확인 합니다.
- [자습서: 장치에서 Azure Stack Edge Pro 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md) 을 기준으로 계산 단계를 **구성** 했습니다. 장치에 연결 된 IoT Hub 리소스, IoT 장치 및 IoT Edge 장치가 있어야 합니다.


## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

다음 단계를 수행 하 여 log analytics 작업 영역을 만듭니다. Log analytics 작업 영역은 로그 데이터를 수집 하 고 저장 하는 논리적 저장소 단위입니다.

1. Azure Portal에서 **+ 리소스 만들기** 를 선택 하 고 **Log Analytics 작업 영역** 을 검색 한 후 **만들기** 를 선택 합니다. 
1. **Log Analytics 만들기 작업 영역** 에서 다음 설정을 구성 합니다. 나머지는 기본값으로 적용 합니다.

    1. **기본 사항** 탭에서 작업 영역에 대 한 구독, 리소스 그룹, 이름 및 지역을 제공 합니다. 

        ![Log Analytics 작업 영역에 대 한 기본 사항 탭](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. **가격 책정 계층** 탭에서 기본 **종 량 제 요금제** 를 적용 합니다.

        ![Log Analytics 작업 영역에 대 한 가격 책정 탭](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. **검토 + 만들기** 탭에서 작업 영역에 대 한 정보를 검토 하 고 **만들기** 를 선택 합니다.

        ![Log Analytics 작업 영역에 대 한 검토 + 만들기](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

자세한 내용은 [Azure Portal를 통해 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)의 자세한 단계를 참조 하세요.



## <a name="enable-container-insights"></a>컨테이너 insights 사용

작업 영역에서 컨테이너 정보를 사용 하도록 설정 하려면 다음 단계를 수행 합니다. 

1. [Azure Monitor 컨테이너 솔루션을 추가](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)하는 방법의 자세한 단계를 따르세요. 다음 템플릿 파일을 사용 합니다 `containerSolution.json` .

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. 리소스 ID 및 위치를 가져옵니다. `Your Log Analytics workspace > General > Properties`로 이동합니다. 다음 정보를 복사 합니다.

    - Azure Log Analytics 작업 영역의 정규화 된 Azure 리소스 ID 인 **리소스 id** 입니다. 
    - **location** 은 Azure 지역입니다.

    ![Log Analytics 작업 영역의 속성](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. 다음 매개 변수 파일을 사용 `containerSolutionParams.json` 합니다. 를 `workspaceResourceId` 리소스 ID로 바꾸고,을 `workspaceRegion` 이전 단계에서 복사한 위치로 바꿉니다.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    컨테이너 Insights를 사용 하도록 설정 된 Log Analytics 작업 영역의 샘플 출력은 다음과 같습니다.
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>장치에서 Azure Monitor 구성

1. 새로 만든 Log Analytics 리소스로 이동 하 여 **작업 영역 ID** 및 **기본 키** (작업 영역 키)를 복사 합니다.

    ![Log Analytics 작업 영역에서 에이전트 관리](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    이후 단계에서 사용할 때이 정보를 저장 합니다.

1. [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다. 
    
1. 다음 cmdlet을 사용 하 여 log analytics 작업 영역 ID 및 작업 영역 키를 사용 합니다.

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Azure Monitor를 사용 하도록 설정 하면 Log Analytics 작업 영역에 로그가 표시 됩니다. 장치에 배포 된 Kubernetes 클러스터의 상태를 보려면 **Azure Monitor > Insights > 컨테이너** 로 이동 합니다. 환경 옵션에 대해 **모두** 를 선택 합니다. 

    ![Log Analytics 작업 영역의 메트릭](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>다음 단계

- [Kubernetes 대시보드를 통해 Kubernetes 워크 로드를 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)하는 방법을 알아봅니다.
- [장치 이벤트 경고 알림을 관리](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)하는 방법을 알아봅니다. 