---
title: AKS(Azure Kubernetes Service) 상태 모니터링(미리 보기) | Microsoft Docs
description: 이 문서에서는 AKS 컨테이너의 성능을 쉽게 검토하여 호스트된 Kubernetes 환경의 사용률을 빠르게 파악하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: magoedte
ms.openlocfilehash: 806487ec731a1b7fe02ccdfe6b285f5b2e119787
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249100"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>AKS(Azure Kubernetes Service) 컨테이너 상태 모니터링(미리 보기)

이 문서에서는 Azure Monitor 컨테이너 상태를 설정하고 사용하여 Kubernetes 환경에 배포되고 AKS(Azure Kubernetes Service)에서 호스트되는 워크로드의 성능을 모니터링하는 방법을 설명합니다. Kubernetes 클러스터 및 컨테이너를 모니터링하는 것은 중요하며, 특히 여러 응용 프로그램을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우 그렇습니다.

컨테이너 상태는 메트릭 API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너에서 메모리 및 프로세서 메트릭을 수집하여 성능 모니터링 기능을 제공합니다. 컨테이너 상태를 사용하도록 설정하면, 이러한 메트릭이 Linux용 OMS(Operations Management Suite) 에이전트의 컨테이너화된 버전을 통해 자동으로 수집된 후 [Log Analytics](../log-analytics/log-analytics-overview.md) 작업 영역에 저장됩니다. 포함된 미리 정의된 보기에는 상주하는 컨테이너 워크로드와 Kubernetes 클러스터의 성능 상태에 영향을 미치는 요소가 포함되기 때문에 다음과 같은 작업이 가능합니다.  

* 노드에서 실행 중인 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 확인합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다. 
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다. 

Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하여 구성, 감사 및 리소스 사용률을 확인하려는 경우 [컨테이너 모니터링 솔루션](../log-analytics/log-analytics-containers.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건 
시작하기 전에 다음 항목이 있는지 확인하십시오.

- 새 또는 기존 AKS 클러스터.
- Linux용 컨테이너화된 OMS 에이전트 microsoft/oms:ciprod04202018 이상의 버전. 버전 번호는 *mmddyyyy* 형식의 날짜로 표시됩니다. 에이전트는 컨테이너 상태를 온보딩하는 동안 자동으로 설치됩니다. 
- Log Analytics 작업 영역. 새 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 때 만들거나 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)나 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 만들 수 있습니다.
- 컨테이너 모니터링을 사용하도록 설정하기 위한 Log Analytics 기여자 역할. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../log-analytics/log-analytics-manage-access.md)를 참조하세요.

## <a name="components"></a>구성 요소 

성능을 모니터링하는 기능은 컨테이너화된 Linux용 OMS 에이전트에 의존합니다. 이를 통해 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집합니다. 이 에이전트는 컨테이너 모니터링을 사용하도록 설정한 후에 지정된 Log Analytics 작업 영역에 자동으로 배포되고 등록됩니다. 

>[!NOTE] 
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다. 
>

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다. 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>새 클러스터에 대해 컨테이너 상태 모니터링 사용
배포하는 동안 Azure Portal에서 새 AKS 클러스터의 모니터링을 사용하도록 설정할 수 있습니다. 빠른 시작 문서 [AKS(Azure Kubernetes Service) 클러스터 배포](../aks/kubernetes-walkthrough-portal.md)의 단계를 따르세요. **모니터링** 페이지에서 **모니터링 사용** 옵션에 대해 **예**를 선택한 다음, 기존 Log Analytics 작업 영역을 선택하거나 새로 만듭니다. 

모니터링을 사용하도록 설정하고 모든 구성 작업이 성공적으로 완료되면 다음 두 가지 방법 중 하나로 클러스터의 성능을 모니터링할 수 있습니다.

* 왼쪽 창에서 **상태**를 선택하여 AKS 클러스터에서 직접 모니터링합니다.
* 선택한 클러스터에 대한 AKS 클러스터 페이지에서 **컨테이너 상태 모니터링** 타일을 선택하고 Azure Monitor의 왼쪽 창에서 **상태**를 선택합니다. 

  ![AKS에서 컨테이너 상태를 선택하는 옵션](./media/monitoring-container-health/container-performance-and-health-select-01.png)

모니터링을 사용하도록 설정한 후 약 15분 후에 클러스터에 대한 운영 데이터를 볼 수 있습니다. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>관리되는 기존 클러스터에 대해 컨테이너 상태 모니터링 사용
PowerShell cmdlet `New-AzureRmResourceGroupDeployment` 또는 Azure CLI를 사용하여 Azure Portal에서 또는 제공된 Azure Resource Manager 템플릿을 통해 이미 배포된 AKS 클러스터의 모니터링을 사용하도록 설정할 수 있습니다. 

### <a name="enable-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링을 사용하도록 설정
Azure Portal에서 AKS 컨테이너에 대한 모니터링을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 
2. 리소스 목록에서 **컨테이너** 입력을 시작합니다.  
    입력한 내용을 기반으로 목록이 필터링됩니다. 
3. **Kubernetes 서비스**를 선택합니다.  

    ![Kubernetes 서비스 링크](./media/monitoring-container-health/azure-portal-01.png)

4. 컨테이너 목록에서 컨테이너를 선택합니다.
5. 컨테이너 개요 페이지에서 **컨테이너 상태 모니터링**을 선택합니다.  
6. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너 상태 및 로그에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 AKS 컨테이너가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다. 

    ![AKS 컨테이너 상태 모니터링 사용](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-create-workspace.md)를 참조하세요. AKS 컨테이너가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다. 
 
모니터링을 사용하도록 설정한 후 약 15분 후에 클러스터에 대한 운영 데이터를 볼 수 있습니다. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 모니터링 사용
이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

* AKS 컨테이너 리소스 ID. 
* 클러스터가 배포된 리소스 그룹.
* Log Analytics 작업 영역 및 작업 영역을 만들 지역. 

Log Analytics 작업 영역은 수동으로 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)나 [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 설정할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

#### <a name="create-and-execute-a-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
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

2. 이 파일을 **existingClusterOnboarding.json**으로 로컬 폴더에 저장합니다.
3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. AKS 클러스터에 대한 **AKS 개요** 페이지에서 찾을 수 있는 값을 사용하여 **aksResourceId** 및 **aksResourceLocation**의 값을 편집합니다. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다. 또한 **workspaceRegion**의 작업 영역 위치를 지정합니다. 
5. 이 파일을 **existingClusterParam.json**으로 로컬 폴더에 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용합니다.

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

        ```powershell
        provisioningState       : Succeeded
        ```

    * Linux에서 Azure CLI를 사용하여 다음 명령을 실행하려면:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

        ```azurecli
        provisioningState       : Succeeded
        ```
모니터링을 사용하도록 설정한 후 약 15분 후에 클러스터에 대한 운영 데이터를 볼 수 있습니다. 

## <a name="verify-agent-and-solution-deployment"></a>에이전트 및 솔루션 배포 확인
에이전트 *06072018* 또는 이후 버전을 사용하여 에이전트 및 솔루션이 모두 성공적으로 배포되었는지 확인할 수 있습니다. 이전 버전의 에이전트를 사용하면 에이전트 배포만 확인할 수 있습니다.

### <a name="agent-version-06072018-or-later"></a>에이전트 06072018 또는 이후 버전
다음 명령을 실행하여 에이전트가 성공적으로 배포되었는지 확인합니다. 

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

솔루션의 배포를 확인하려면 다음 명령을 실행합니다.

```
kubectl get deployment omsagent-rs -n=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 이전 에이전트 버전

*06072018* 이전에 릴리스된 OMS 에이전트 버전이 제대로 배포되었는지 확인하려면 다음 명령을 실행합니다.  

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>성능 사용률 보기
컨테이너 상태를 열면 페이지에는 즉시 전체 클러스터의 성능 사용률이 표시됩니다. AKS 클러스터에 대한 정보 보기는 다음 네 가지 관점으로 구성됩니다.

- 프로비전
- 노드 
- Controllers  
- 컨테이너

**클러스터** 탭의 네 가지 줄 성능 차트에 클러스터의 주요 성능 메트릭이 표시됩니다. 

![클러스터 탭의 예제 성능 차트](./media/monitoring-container-health/container-health-cluster-perfview.png)

성능 차트에는 네 가지 성능 메트릭이 표시됩니다.

- **노드 CPU 사용률&nbsp;%**: 전체 클러스터에 대한 CPU 사용률의 집계된 큐브 뷰를 나타냅니다. 차트 위에 있는 백분위 선택기에서 개별적이거나 결합된 **평균**, **최소**, **최대**, **50번째**, **90번째** 및 **95번째**를 선택하여 시간 범위에 대한 결과를 필터링할 수 있습니다. 
- **노드 메모리 사용률&nbsp;%**: 이 차트는 전체 클러스터에 대한 메모리 사용률의 집계된 큐브 뷰를 나타냅니다. 차트 위에 있는 백분위 선택기에서 개별적이거나 결합된 **평균**, **최소**, **최대**, **50번째**, **90번째** 및 **95번째**를 선택하여 시간 범위에 대한 결과를 필터링할 수 있습니다. 
- **노드 수**: Kubernetes의 노드 수 및 상태입니다. 표시되는 클러스터 노드의 상태는 *모두*, *준비됨* 및 *준비되지 않음*이고 차트 위의 선택기에서 개별적이거나 결합되어 필터링될 수 있습니다. 
- **작업 Pod 수**: Kubernetes의 Pod 수 및 상태입니다. 표시되는 Pod의 상태는 *모두*, *보류 중*, *실행 중* 및 *알 수 없음*이고 차트 위의 선택기에서 개별적이거나 결합되어 필터링될 수 있습니다. 

**노드** 탭으로 전환하면 행 계층 구조는 클러스터의 노드로 시작하는 Kubernetes 개체 모델을 따릅니다. 노드를 확장하면 노드에서 실행 중인 하나 이상의 창을 볼 수 있습니다. 하나 이상의 컨테이너가 Pod로 그룹화된 경우 계층 구조에서 마지막 행으로 표시됩니다. 호스트에 프로세서 또는 메모리 부족 문제가 있는 경우 호스트에서 실행 중인 Pod와 관련되지 않은 워크로드의 수를 볼 수도 있습니다.

![성능 보기의 Kubernetes 노드 계층 예제](./media/monitoring-container-health/container-health-nodes-view.png)

페이지의 위쪽에서 컨트롤러 또는 컨테이너를 선택하고, 해당 개체에 대한 상태 및 리소스 사용률을 검토할 수 있습니다. 맨 위에 있는 드롭다운 상자를 사용하여 네임스페이스, 서비스 및 노드별로 필터링할 수 있습니다. 대신, 메모리 사용률을 검토하려면 **메트릭** 드롭다운 목록에서 **메모리 RSS** 또는 **메모리 작업 집합**을 선택합니다. **메모리 RSS**는 Kubernetes 버전 1.8 이상에서만 지원됩니다. 그렇지 않으면 **Min&nbsp;%** 에 대한 값이 *NaN&nbsp;%* 로 표시됩니다. 이것은 정의되지 않았거나 표현할 수 없는 값을 나타내는 숫자 데이터 형식 값입니다. 

![컨테이너 노드 성능 보기](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

기본적으로 성능 데이터는 마지막 6시간을 기준으로 하지만, 오른쪽 위에 있는 **시간 범위** 드롭다운 목록을 사용하여 이 기간을 변경할 수 있습니다. 현재, 이 페이지는 자동으로 새로 고쳐지지 않으므로 수동으로 새로 고쳐야 합니다. 백분위 선택기에서 **평균**, **최소**, **최대**, **50번째**, **90번째** 및 **95번째**를 선택하여 시간 범위 내에서 결과를 필터링할 수도 있습니다. 

![데이터 필터링에 대한 백분위 선택 영역](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

다음 예제에서는 노드 *aks-nodepool-3977305*의 **컨테이너** 값이 5이며, 이것은 배포된 전체 컨테이너 수를 롤업한 값입니다.

![노드당 컨테이너 롤업 예제](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

이를 통해 클러스터의 노드 간에 컨테이너가 적절히 분산되어 있는지 여부를 빠르게 알 수 있습니다. 

노드를 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 호스트의 이름입니다. |
| 상태 | 노드 상태의 Kubernetes 보기입니다. |
| 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;% | 선택한 기간 동안 백분위에 기반한 평균 노드 백분율입니다. |
| 평균, 최소, 최대, 50번째, 90번째 | 선택한 기간 동안 백분위를 기준으로 하는 평균 노드 실제 값입니다. 평균 값은 노드에 대해 설정된 CPU/메모리 제한에서 측정됩니다. Pod 및 컨테이너의 경우에는 호스트에서 보고된 평균 값입니다. |
| 컨테이너 | 컨테이너의 수입니다. |
| 작동 시간 | 노드가 시작되었거나 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| Controllers | 컨테이너 및 Pod용입니다. 어떤 컨트롤러가 상주하는지 보여줍니다. 모든 Pod가 컨트롤러에 있는 것은 아니므로 **N/A**가 표시될 수 있습니다. | 
| 추세 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;% | 컨트롤러의 백분위 메트릭 비율을 나타내는 막대 그래프 추세입니다. |


선택기에서 **컨트롤러**를 선택합니다.

![컨트롤러 보기 선택](./media/monitoring-container-health/container-health-controllers-tab.png)

여기에서 컨트롤러의 성능 상태를 볼 수 있습니다.

![<Name> 컨트롤러 성능 보기](./media/monitoring-container-health/container-health-controllers-view.png)

행 계층 구조는 컨트롤러로 시작하고 컨트롤러를 확장하며 하나 이상의 컨테이너가 표시됩니다. Pod를 확장하면 마지막 행에 Pod로 그룹화된 컨테이너가 표시됩니다. 

컨트롤러를 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | *확인*, *종료됨*, *실패함*, *중지됨* 또는 *일시 정지됨*과 같은 상태로 실행이 완료된 경우의 컨테이너 롤업 상태입니다. 컨테이너가 실행 중이지만 상태가 제대로 표시되지 않았거나 에이전트에 의해 선택되지 않았고 30분 넘게 응답하지 않은 경우 상태는 *알 수 없음*이 됩니다. 상태 아이콘에 대한 자세한 내용은 아래 표에 나와 있습니다.|
| 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;% | 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 평균 롤업입니다. |
| 평균, 최소, 최대, 50번째, 90번째  | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| 컨테이너 | 컨트롤러 또는 Pod에 대한 컨테이너의 총 수입니다. |
| Restarts | 컨테이너에서 다시 시작한 횟수의 롤업입니다. |
| 작동 시간 | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 노드 | 컨테이너 및 Pod용입니다. 컨테이너 또는 Pod가 있는 컨트롤러를 표시합니다. | 
| 추세 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;%| 컨트롤러의 백분위 메트릭을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 컨테이너의 온라인 상태를 나타냅니다.
 
| 아이콘 | 상태 | 
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/monitoring-container-health/container-health-ready-icon.png) | 실행 중(준비됨)|
| ![대기 중 또는 일시 중지됨 상태 아이콘](./media/monitoring-container-health/container-health-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/monitoring-container-health/container-health-grey-icon.png) | 하지만 마지막으로 보고된 실행 중 상태는 30분 넘게 응답되지 않았습니다.|
| ![성공 상태 아이콘](./media/monitoring-container-health/container-health-green-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|

상태 아이콘은 Pod에서 제공하는 것에 따라 개수를 보여줍니다. 나쁜 두 가지 상태를 표시하고 상태 위로 마우스를 가져가면 컨테이너에 있는 모든 Pod의 롤업 상태를 표시합니다. 준비 상태가 아니면 상태 값은 **(0)** 을 표시합니다. 

선택기에서 **컨테이너**를 선택합니다.

![컨테이너 보기 선택](./media/monitoring-container-health/container-health-containers-tab.png)

여기에서 컨테이너의 성능 상태를 볼 수 있습니다.

![<Name> 컨트롤러 성능 보기](./media/monitoring-container-health/container-health-containers-view.png)

컨테이너를 볼 때 표시되는 정보는 다음 표에 설명되어 있습니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | 컨테이너의 상태입니다(있는 경우). 상태 아이콘에 대한 자세한 내용은 아래 표에 나와 있습니다.|
| 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;% | 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 롤업입니다. |
| 평균, 최소, 최대, 50번째, 90번째  | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| Pod | Pod가 위치한 컨테이너입니다.| 
| 노드 |  컨테이너가 있는 노드입니다. | 
| Restarts | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 작동 시간 | 컨테이너가 시작 또는 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| 추세 평균&nbsp;%, 최소&nbsp;%, 최대&nbsp;%, 50번째&nbsp;%, 90번째&nbsp;% | 컨테이너의 평균 메트릭 백분율을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 다음 표에 설명된 대로 Pod의 온라인 상태를 나타냅니다.
 
| 아이콘 | 상태 | 
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/monitoring-container-health/container-health-ready-icon.png) | 실행 중(준비됨)|
| ![대기 중 또는 일시 중지됨 상태 아이콘](./media/monitoring-container-health/container-health-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/monitoring-container-health/container-health-grey-icon.png) | 하지만 마지막으로 보고된 실행 중 상태는 30분 넘게 응답되지 않았습니다.|
| ![종료됨 상태 아이콘](./media/monitoring-container-health/container-health-terminated-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|
| ![실패 상태 아이콘](./media/monitoring-container-health/container-health-failed-icon.png) | 실패 상태 |

## <a name="container-data-collection-details"></a>컨테이너 데이터 컬렉션 세부 정보
컨테이너 상태는 컨테이너 호스트 및 컨테이너로부터 다양한 성능 메트릭과 로그 데이터를 수집합니다. 데이터는 3분마다 수집됩니다.

### <a name="container-records"></a>컨테이너 레코드

컨테이너 상태별로 수집된 레코드 및 로그 검색 결과에 나타나는 데이터 유형의 예가 다음 표에 나와 있습니다.

| 데이터 형식 | 로그 검색의 데이터 유형 | 필드 |
| --- | --- | --- |
| 호스트 및 컨테이너에 대한 성능 | `Perf` | 컴퓨터, ObjectName, CounterName &#40;%프로세서 시간, 디스크 읽기 MB, 디스크 쓰기 MB, 메모리 사용 MB, 네트워크 수신 바이트, 네트워크 송신 바이트, 프로세서 사용 초, 네트워크&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| 컨테이너 인벤토리 | `ContainerInventory` | TimeGenerated, 컴퓨터, 컨테이너 이름, ContainerHostname, 이미지, ImageTag, ContainerState, ExitCode, EnvironmentVar, 명령, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| 컨테이너 이미지 인벤토리 | `ContainerImageInventory` | TimeGenerated, 컴퓨터, 이미지, ImageTag, ImageSize, VirtualSize, 실행 중, 일시 중지됨, 중지됨, 실패, SourceSystem, ImageID, TotalContainer |
| 컨테이너 로그 | `ContainerLog` | TimeGenerated, 컴퓨터, 이미지 ID, 컨테이너 이름, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| 컨테이너 서비스 로그 | `ContainerServiceLog`  | TimeGenerated, 컴퓨터, TimeOfCommand, 이미지, 명령, SourceSystem, ContainerID |
| 컨테이너 노드 인벤토리 | `ContainerNodeInventory_CL`| TimeGenerated, 컴퓨터, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| 컨테이너 프로세스 | `ContainerProcess_CL` | TimeGenerated, 컴퓨터, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes 클러스터의 Pod 인벤토리 | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Kubernetes 클러스터의 노드 부분 인벤토리 | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 이벤트 | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 클러스터의 서비스 | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 클러스터의 노드 부분에 대한 성능 메트릭 | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Kubernetes 클러스터의 컨테이너 부분에 대한 성능 메트릭 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>로그를 검색하여 데이터 분석
Log Analytics에서는 현재 클러스터 구성이 최적 상태로 실행되고 있는지를 파악하는 데 도움이 되도록 추세를 찾아보거나, 병목 상태를 진단하거나, 예측하거나 데이터 간 상관 관계를 파악할 수 있습니다. 미리 정의된 로그 검색을 즉시 사용하거나, 원하는 방식으로 정보를 반환하도록 사용자 지정할 수 있습니다. 

컨트롤러 또는 컨테이너를 확장하면 맨 오른쪽에 있는 **로그 보기** 옵션을 선택하여 작업 영역에서 데이터를 대화형으로 분석할 수 있습니다. 사용자가 있던 Azure Portal 페이지 위에 **로그 검색** 페이지가 나타납니다.

![Log Analytics에서 데이터 분석](./media/monitoring-container-health/container-health-view-logs.png)   

Log Analytics에 전달되는 컨테이너 로그 출력은 STDOUT 및 STDERR입니다. 컨테이너 상태는 AKS(Azure-managed Kubernetes)를 모니터링하고 이로 인해 생성되는 데이터 양이 많으므로 Kube 시스템은 오늘 수집되지 않습니다. 

### <a name="example-log-search-queries"></a>로그 검색 쿼리 예제
한두 가지 예제로 시작하는 쿼리를 작성한 다음, 요구 사항에 맞게 수정하는 것이 유용한 경우가 많습니다. 고급 쿼리를 작성하는 데 도움이 되도록 다음과 같은 샘플 쿼리를 테스트해 볼 수 있습니다.

| 쿼리 | 설명 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 컨테이너의 수명 주기 정보 모두 나열| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | kubernetes 이벤트|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 이미지 인벤토리 | 
| **고급 분석에서 꺾은선형 차트를 선택합니다**.<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 CPU | 
| **고급 분석에서 꺾은선형 차트를 선택합니다**.<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 메모리 |

## <a name="how-to-stop-monitoring-with-container-health"></a>컨테이너의 상태를 사용하여 모니터링을 중지하는 방법
AKS 컨테이너에 대한 모니터링을 사용하도록 설정한 후에 더 이상 모니터링하지 않기로 결정한 경우 Azure CLI 또는 PowerShell cmdlet **New-AzureRmResourceGroupDeployment**에서 제공된 Azure Resource Manager 템플릿을 사용하여 *옵트아웃(opt out)* 할 수 있습니다. 하나의 JSON 템플릿은 *옵트아웃(opt out)* 할 구성을 지정합니다. 다른 템플릿에는 클러스터가 배포된 AKS 클러스터 리소스 ID 및 리소스 그룹을 지정하도록 구성하는 매개 변수 값이 포함됩니다. 

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

### <a name="create-and-execute-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. 이 파일을 로컬 폴더에 **OptOutTemplate.json**으로 저장합니다.
3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. 선택한 클러스터에 대한 **속성** 페이지에서 찾을 수 있는 AKS 클러스터 값을 사용하여 **aksResourceId** 및 **aksResourceLocation**의 값을 편집합니다.

    ![컨테이너 속성 페이지](./media/monitoring-container-health/container-properties-page.png)

    **속성** 페이지에서 **작업 영역 리소스 ID**도 복사합니다. 이 값은 나중에 Log Analytics 작업 영역을 삭제하려는 경우 필요합니다. Log Analytics 작업 영역을 삭제하는 작업은 이 단계에서는 수행되지 않습니다. 

5. 이 파일을 로컬 폴더에 **OptOutParam.json**으로 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용하려면:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Linux에서 Azure CLI를 사용하여 다음 명령을 실행하려면

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

        ```azurecli
        ProvisioningState       : Succeeded
        ```

클러스터 모니터링을 지원하려는 목적으로만 작업 영역을 만들었으며 더 이상 필요하지 않은 경우 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법을 모를 경우 [Azure Portal에서 Azure Log Analytics 작업 영역 삭제](../log-analytics/log-analytics-manage-del-workspace.md)를 참조하세요. 이전에 4단계에서 복사한 **작업 영역 리소스 ID**는 나중에 필요하므로 기억해두세요. 

## <a name="troubleshooting"></a>문제 해결
이 섹션에서는 컨테이너 상태와 관련된 문제 해결에 도움이 되는 정보를 제공합니다.

컨테이너 상태를 사용하도록 설정과 구성이 완료되었지만 로그 검색을 수행할 때 Log Analytics에 상태 정보나 결과가 표시되지 않으면 다음을 수행하여 문제를 진단할 수 있습니다. 

1. 다음 명령을 실행하여 에이전트의 상태를 확인합니다. 

    `kubectl get ds omsagent --namespace=kube-system`

    출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 에이전트 *06072018* 이후 버전에서 다음 명령을 실행하여 솔루션 배포 상태를 확인합니다.

    `kubectl get deployment omsagent-rs -n=kube-system`

    출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. `kubectl get pods --namespace=kube-system` 명령을 실행하여 Pod의 상태를 확인하고 실행 중인지 확인합니다.

    출력은 다음과 비슷하며 omsagent의 상태는 *Running*입니다.

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 에이전트 로그를 확인합니다. 컨테이너화된 에이전트가 배포되면 OMI 명령을 실행하여 빠른 검사가 실행되고 에이전트 및 공급자의 버전이 표시됩니다. 

5. 에이전트가 성공적으로 온보드되었는지 확인하려면 다음 명령을 실행합니다. `kubectl logs omsagent-484hw --namespace=kube-system`

    상태는 다음과 유사합니다.

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>다음 단계

자세한 컨테이너 상태 및 응용 프로그램 성능 정보를 확인하려면 [로그 검색](../log-analytics/log-analytics-log-search.md)을 참조하세요. 
