---
title: 컨테이너에 대한 Azure 모니터로 하이브리드 Kubernetes 클러스터 구성 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 스택 또는 기타 환경에서 호스팅되는 Kubernetes 클러스터를 모니터링하도록 컨테이너에 대한 Azure Monitor를 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198057"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 통해 하이브리드 Kubernetes 클러스터 구성

컨테이너용 Azure 모니터는 Azure에서 호스팅되는 자체 관리 Kubernetes 클러스터인 Azure Kubernetes 서비스(AKS) 및 [AKS 엔진에](https://github.com/Azure/aks-engine)대한 풍부한 모니터링 환경을 제공합니다. 이 문서에서는 Azure 외부에서 호스팅되는 Kubernetes 클러스터를 모니터링하고 유사한 모니터링 환경을 달성하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

* Log Analytics 작업 영역.

    컨테이너용 Azure Monitor는 지역별로 Azure [Products에](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)나열된 리전에서 로그 분석 작업 영역을 지원합니다. 사용자 고유의 작업 영역을 만들려면 [Azure 리소스 관리자를](../platform/template-workspace-configuration.md)통해 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure 포털에서](../learn/quick-create-workspace.md)만들 수 있습니다.

    >[!NOTE]
    >동일한 클러스터 이름을 가진 여러 클러스터를 동일한 Log Analytics 작업 영역에 모니터링할 수 있도록 지원되지 않습니다. 클러스터 이름은 고유해야 합니다.
    >

* 컨테이너 모니터링을 사용하도록 설정하는 **Log Analytics 기여자 역할의** 구성원입니다. 로그 애널리틱스 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 및 로그 데이터에 대한 액세스 관리를](../platform/manage-access.md) 참조하십시오.

* [HELM 클라이언트는](https://helm.sh/docs/using_helm/) 지정된 Kubernetes 클러스터에 대한 컨테이너 차트에 대한 Azure 모니터를 온보보드합니다.

* Linux용 Log Analytics 에이전트의 컨테이너화된 버전이 Azure Monitor와 통신하려면 다음 프록시 및 방화벽 구성 정보가 필요합니다.

    |에이전트 리소스|포트 |
    |------|---------|   
    |*.ods.opinsights.azure.com |포트 443 |  
    |*.oms.opinsights.azure.com |포트 443 |  
    |\*.blob.core.windows.net |포트 443 |  
    |*.dc.services.visualstudio.com |포트 443 |

* 컨테이너화된 에이전트는 성능 메트릭을 `cAdvisor secure port: 10250` `unsecure port :10255` 수집하기 위해 클러스터의 모든 노드에서 Kubelet또는 열어야 합니다. 아직 구성되지 `secure port: 10250` 않은 경우 Kubelet의 cAdvisor에서 구성하는 것이 좋습니다.

* 컨테이너화된 에이전트는 인벤토리 데이터를 수집하기 위해 클러스터 내의 Kubernetes API 서비스와 통신하기 위해 `KUBERNETES_SERVICE_HOST` 컨테이너에 다음 환경 변수를 지정해야 합니다. `KUBERNETES_PORT_443_TCP_PORT`

>[!IMPORTANT]
>하이브리드 Kubernetes 클러스터모니터링을 위해 지원되는 최소 에이전트 버전은 ciprod10182019 이상입니다.

## <a name="supported-configurations"></a>지원되는 구성

다음은 컨테이너에 대한 Azure 모니터에서 공식적으로 지원됩니다.

- 환경: 온-프레미스, Azure 및 Azure 스택의 AKS 엔진. 자세한 내용은 [Azure 스택의 AKS 엔진을](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)참조하십시오.
- Kubernetes 및 지원 정책의 버전은 [지원되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일합니다.
- 컨테이너 런타임: 도커 및 모비
- 마스터 및 작업 노드에 대 한 리눅스 OS 릴리스: 우분투 (18.04 LTS 및 16.04 LTS)
- 액세스 제어 지원: Kubernetes RBAC 및 비 RBAC

## <a name="enable-monitoring"></a>모니터링 사용

하이브리드 Kubernetes 클러스터에 대한 컨테이너에 대한 Azure 모니터 를 사용하도록 설정하는 것은 다음 단계를 순서대로 수행하는 것으로 구성됩니다.

1. 컨테이너 인사이트 솔루션으로 로그 분석 작업 영역을 구성합니다.

2. 로그 분석 작업 영역이 있는 컨테이너 HELM 차트에 대한 Azure 모니터를 활성화합니다.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure 모니터 컨테이너 솔루션을 추가하는 방법

Azure PowerShell cmdlet `New-AzResourceGroupDeployment` 또는 Azure CLI를 사용하여 제공된 Azure 리소스 관리자 템플릿을 사용하여 솔루션을 배포할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- **workspaceResourceId** - 로그 분석 작업 영역의 전체 리소스 ID입니다.
- **작업 영역Region** - 작업 영역이 만들어지는 영역으로 Azure 포털에서 볼 때 작업 영역 속성의 **위치라고도** 합니다.

먼저 컨테이너의 `workspaceResourceId` 매개 변수 값에 필요한 로그 분석 작업 영역의 전체 리소스 ID를 식별하려면SolutionParams.json 파일에서 다음 단계를 수행한 다음 PowerShell cmdlet 또는 Azure CLI 명령을 실행하여 솔루션을 추가합니다. **containerSolutionParams.json**

1. 다음 명령을 사용하여 액세스할 수 있는 모든 구독을 나열합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    구독Id 에 대한 값을 **복사합니다.**

2. 다음 명령을 사용하여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예제에서는 구독의 작업 영역 목록을 기본 JSON 형식으로 표시합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음 필드 **ID**에서 해당 Log Analytics 작업 영역의 전체 리소스 ID를 복사합니다.

4. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
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

5. 이 파일을 컨테이너로 저장솔루션.json을 로컬 폴더에 저장합니다.

6. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 3단계에서 복사한 값을 사용하여 **workspaceResourceId의** 값을 편집하고 **workspaceRegion은** Azure CLI 명령 az [모니터 로그 분석 작업 영역 을](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)실행한 후 **지역** 값을 복사합니다.

8. 이 파일을 컨테이너로 저장솔루션Params.json을 로컬 폴더에 저장합니다.

9. 이제 이 템플릿을 배포할 수 있습니다.

   * Azure PowerShell을 사용하여 배포하려면 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLI를 사용하여 배포하려면 다음 명령을 실행합니다.

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```azurecli
       provisioningState       : Succeeded
       ```

       모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

## <a name="install-the-chart"></a>차트 설치

HELM 차트를 사용하려면 다음을 수행합니다.

1. 다음 명령을 실행하여 로컬 목록에 Azure 차트 리포지토리를 추가합니다.

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. 다음 명령을 실행하여 차트를 설치합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    로그 분석 작업 영역이 Azure 중국에 있는 경우 다음 명령을 실행합니다.

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    로그 분석 작업 영역이 Azure 미국 정부에 있는 경우 다음 명령을 실행합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

차트 버전 1.0.0을 사용하면 에이전트 데이터 수집 설정이 ConfigMap에서 제어됩니다. 에이전트 데이터 수집 설정에 대한 [설명서를 참조하려면 여기를](container-insights-agent-config.md)참조하십시오.

차트를 성공적으로 배포한 후에는 Azure Portal의 컨테이너에 대해 Azure Monitor에서 하이브리드 Kubernetes 클러스터에 대한 데이터를 검토할 수 있습니다.  

>[!NOTE]
>수집 대기 시간은 에이전트에서 Azure Log Analytics 작업 영역에서 커밋까지 약 5~10분입니다. 클러스터 의 상태는 Azure Monitor에서 필요한 모든 모니터링 데이터를 사용할 수 있는 때까지 **데이터 없음** 또는 알 **수 없는** 값을 표시합니다.

## <a name="troubleshooting"></a>문제 해결

하이브리드 Kubernetes 클러스터에 대한 모니터링을 사용하도록 설정하는 동안 오류가 발생하면 PowerShell 스크립트 [TroubleshootError_nonAzureK8s.ps1을](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) 복사하여 컴퓨터의 폴더에 저장합니다. 이 스크립트는 발생한 문제를 감지하고 해결하는 데 도움이 되는 데 도움이 되는 제공됩니다. 이를 감지하고 수정을 시도하도록 설계된 문제는 다음과 같습니다.

* 지정된 로그 분석 작업 영역이 유효합니다.
* 로그 분석 작업 영역은 컨테이너용 Azure 모니터 솔루션으로 구성됩니다. 그렇지 않은 경우 작업 영역을 구성합니다.
* OmsAgent 복제 집합 포드가 실행 중입니다.
* OmsAgent 데몬셋 포드가 실행 중
* OmsAgent 상태 서비스가 실행 중입니다.
* 컨테이너화된 에이전트에 구성된 Log Analytics 작업 영역 ID와 키는 Insight가 구성된 작업 영역과 일치합니다.
* rs 포드를 예약하는 `kubernetes.io/role=agent` 레이블이 있는 모든 Linux 작업자 노드의 유효성을 검사합니다. 존재하지 않는 경우 추가합니다.
* 클러스터의 `unsecure port: 10255` 모든 노드에서 유효성을 검사하거나 `cAdvisor secure port:10250` 열립니다.

Azure PowerShell을 사용하여 실행하려면 스크립트가 포함된 폴더의 다음 명령을 사용합니다.

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>다음 단계

모니터링을 사용하여 하이브리드 Kubernetes 클러스터 및 워크로드에서 실행되는 상태 및 리소스 활용도를 수집할 수 있으므로 컨테이너에 Azure [Monitor를 사용하는 방법을](container-insights-analyze.md) 알아봅니다.
