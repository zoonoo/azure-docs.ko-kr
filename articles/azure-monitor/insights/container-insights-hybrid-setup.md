---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 하이브리드 Kubernetes 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 Azure Stack 또는 기타 환경에서 호스트 되는 Kubernetes 클러스터를 모니터링 하도록 컨테이너에 Azure Monitor를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d481af07013c0a5b4c5a381527c6f555400a2559
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890465"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 하이브리드 Kubernetes 클러스터 구성

컨테이너에 대 한 Azure Monitor는 azure에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터 인 azure의 AKS (Azure Kubernetes Service) 및 [AKS Engine](https://github.com/Azure/aks-engine)에 대 한 풍부한 모니터링 환경을 제공 합니다. 이 문서에서는 Azure 외부에서 호스트 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정 하 고 비슷한 모니터링 환경을 구현 하는 방법을 설명 합니다.

## <a name="supported-configurations"></a>지원되는 구성

다음 구성은 컨테이너에 대 한 Azure Monitor에서 공식적으로 지원 됩니다. 다른 버전의 Kubernetes 및 운영 체제 버전을 사용할 경우로 메일을 보내 주시기 바랍니다 askcoin@microsoft.com .

- 에서는

    - Kubernetes 온-프레미스
    - Azure의 AKS 엔진과 Azure Stack. 자세한 내용은 [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908&preserve-view=true) 을 참조 하세요.
    - [Openshift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 버전 4 이상, 온-프레미스 또는 기타 클라우드 환경.

- Kubernetes 및 지원 정책의 버전은 [지원 되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일 합니다.

- 다음 컨테이너 런타임이 지원 됩니다. Docker, Moby 및 CRI compatible runtime (CRI 및 ContainerD).

- 지원 되는 마스터 및 작업자 노드에 대 한 Linux OS 릴리스는 Ubuntu (18.04 LTS 및 16.04 LTS) 및 Red Hat Enterprise Linux CoreOS 43.81입니다.

- 지원 되는 액세스 제어: Kubernetes RBAC 및 비 RBAC

## <a name="prerequisites"></a>사전 준비 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

- [Log Analytics 작업 영역](../platform/design-logs-deployment.md)

    컨테이너 Azure Monitor는 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열 된 지역에서 Log Analytics 작업 영역을 지원 합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../samples/resource-manager-workspace.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)또는 [Azure Portal](../learn/quick-create-workspace.md)를 통해 만들 수 있습니다.

    >[!NOTE]
    >동일한 Log Analytics 작업 영역에 동일한 클러스터 이름을 가진 여러 클러스터의 모니터링을 사용 하도록 설정할 수 없습니다. 클러스터 이름은 고유 해야 합니다.
    >

- 컨테이너 모니터링을 사용 하도록 설정 하는 **Log Analytics 참여자 역할** 의 구성원입니다. Log Analytics 작업 영역에 대 한 액세스를 제어 하는 방법에 대 한 자세한 내용은 [작업 영역 및 로그 데이터에 대 한 액세스 관리](../platform/manage-access.md)를 참조 하세요.

- 모니터링 데이터를 보려면 Log Analytics 작업 영역에서 컨테이너에 대 한 Azure Monitor 구성 된 [*Log Analytics 읽기 권한자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할이 있어야 합니다.

- 지정 된 Kubernetes 클러스터에 대 한 컨테이너 차트의 Azure Monitor를 등록 하는 [클라이언트](https://helm.sh/docs/using_helm/) 를 작성 합니다.

- 다음 프록시 및 방화벽 구성 정보는 Linux 용 Log Analytics 에이전트의 컨테이너 화 된 버전이 Azure Monitor와 통신 하는 데 필요 합니다.

    |에이전트 리소스|포트 |
    |------|---------|
    |*.ods.opinsights.azure.com |포트 443 |
    |*.oms.opinsights.azure.com |포트 443 |
    |*. dc.services.visualstudio.com |포트 443 |

- 컨테이너 화 된 에이전트는 `cAdvisor secure port: 10250` `unsecure port :10255` 성능 메트릭을 수집 하기 위해 클러스터의 모든 노드에서 Kubelet 또는를 열어야 합니다. `secure port: 10250`아직 구성 되지 않은 경우 Kubelet의 cAdvisor에서 구성 하는 것이 좋습니다.

- 컨테이너 화 된 에이전트는 인벤토리 데이터를 수집 하기 위해 클러스터 내의 Kubernetes API 서비스와 통신 하기 위해 컨테이너에서 다음 환경 변수를 지정 해야 합니다 `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>하이브리드 Kubernetes 클러스터 모니터링에 대해 지원 되는 최소 에이전트 버전은 ciprod10182019 이상입니다.

## <a name="enable-monitoring"></a>모니터링 사용

하이브리드 Kubernetes 클러스터의 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 하는 것은 다음 단계를 순서 대로 수행 하는 것입니다.

1. Container Insights 솔루션을 사용 하 여 Log Analytics 작업 영역을 구성 합니다.   

2. Log Analytics 작업 영역을 사용 하 여 컨테이너 투구 차트에 Azure Monitor을 사용 하도록 설정 합니다.

Azure Monitor의 모니터링 솔루션에 대 한 자세한 내용은 [여기](../../azure-monitor/insights/solutions.md)를 참조 하세요.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Monitor 컨테이너 솔루션을 추가 하는 방법

Azure PowerShell cmdlet 또는 Azure CLI를 사용 하 여 제공 된 Azure Resource Manager 템플릿으로 솔루션을 배포할 수 있습니다 `New-AzResourceGroupDeployment` .

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행 해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- **workspaceResourceId** -Log Analytics 작업 영역의 전체 리소스 ID입니다.
- **workspaceRegion** -작업 영역을 만들 지역으로, Azure Portal에서 볼 때 작업 영역 속성에서 **위치** 라고도 합니다.

먼저containerSolutionParams.js파일의 매개 변수 값에 필요한 Log Analytics 작업 영역의 전체 리소스 ID를 식별 하려면 `workspaceResourceId` 다음 단계를 수행한 후 PowerShell cmdlet 또는 Azure CLI 명령을 실행 하 여 솔루션을 추가 합니다. **containerSolutionParams.json**

1. 다음 명령을 사용 하 여 액세스 권한이 있는 모든 구독을 나열 합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    **SubscriptionId** 의 값을 복사 합니다.

2. 다음 명령을 사용 하 여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환 합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예에서는 구독의 작업 영역 목록을 기본 JSON 형식으로 표시 합니다.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID** 로 복사 합니다.

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

5. 이 파일을 로컬 폴더에 containerSolution.js로 저장 합니다.

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

7. 3 단계에서 복사한 값을 사용 하 여 **workspaceResourceId** 에 대 한 값을 편집 하 고 **workspaceRegion** 의 경우 Azure CLI 명령 [az monitor log-analytics 작업 영역 표시](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list&preserve-view=true)를 실행 한 후 **지역** 값을 복사 합니다.

8. 이 파일을 로컬 폴더에 containerSolutionParams.js로 저장 합니다.

9. 이제 이 템플릿을 배포할 수 있습니다.

   - Azure PowerShell를 사용 하 여 배포 하려면 템플릿이 포함 된 폴더에서 다음 명령을 사용 합니다.

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```powershell
       provisioningState       : Succeeded
       ```

   - Azure CLI를 사용 하 여 배포 하려면 다음 명령을 실행 합니다.

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```azurecli
       provisioningState       : Succeeded
       ```

       모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

## <a name="install-the-helm-chart"></a>투구 차트 설치

이 섹션에서는 컨테이너의 Azure Monitor에 대 한 컨테이너 화 된 에이전트를 설치 합니다. 계속 하기 전에 매개 변수에 필요한 작업 영역 ID `omsagent.secret.wsid` 및 매개 변수에 필요한 기본 키를 확인 해야 합니다 `omsagent.secret.key` . 다음 단계를 수행 하 여이 정보를 확인 한 다음, 해당 명령을 실행 하 여 투구 차트를 사용 하 여 에이전트를 설치할 수 있습니다.

1. 다음 명령을 실행 하 여 작업 영역 ID를 확인 합니다.

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    출력의 필드 **이름** 아래에서 작업 영역 이름을 찾은 다음 해당 Log Analytics 작업 영역의 작업 영역 ID를 필드 **customerID** 아래에 복사 합니다.

2. 다음 명령을 실행 하 여 작업 영역에 대 한 기본 키를 식별 합니다.

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    출력에서 **Primarysharedkey** 필드 아래에 있는 기본 키를 찾은 다음 값을 복사 합니다.

>[!NOTE]
>다음 명령은 투구 버전 2에만 적용 됩니다. 매개 변수를 사용 하는 `--name` 것은 투구 버전 3에는 적용 되지 않습니다. 

>[!NOTE]
>Kubernetes 클러스터가 프록시 서버를 통해 통신 하는 경우 프록시 서버의 URL을 사용 하 여 매개 변수를 구성 `omsagent.proxy` 합니다. 클러스터가 프록시 서버를 통해 통신 하지 않는 경우이 매개 변수를 지정할 필요가 없습니다. 자세한 내용은이 문서의 뒷부분에 있는 [프록시 끝점 구성](#configure-proxy-endpoint) 을 참조 하세요.

3. 다음 명령을 실행 하 여 Azure 차트 리포지토리를 로컬 목록에 추가 합니다.

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. 다음 명령을 실행 하 여 차트를 설치 합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure 중국 21Vianet에 있는 경우 다음 명령을 실행 합니다.

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure US Government에 있는 경우 다음 명령을 실행합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>API 모델을 사용 하 여 투구 차트 사용

API 모델이 라고도 하는 AKS 엔진 클러스터 사양 json 파일에서 추가 기능을 지정할 수 있습니다. 이 추가 기능에서 `WorkspaceGUID` `WorkspaceKey` 수집 된 모니터링 데이터가 저장 되는 Log Analytics 작업 영역 및의 base64 인코딩 버전을 제공 합니다. `WorkspaceGUID` `WorkspaceKey` 이전 섹션에서 1 단계와 2 단계를 사용 하 여 및를 찾을 수 있습니다.

Azure Stack 허브 클러스터에 대해 지원 되는 API 정의는이 예제에서 [kubernetes-container-monitoring_existing_workspace_id_and_key.js에](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)있습니다. 특히 **kubernetesConfig** 에서 **addons** 속성을 찾습니다.

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

바랄 차트 버전 1.0.0에서 에이전트 데이터 수집 설정은 ConfigMap에서 제어 됩니다. [여기](container-insights-agent-config.md)에서 에이전트 데이터 컬렉션 설정에 대 한 설명서를 참조 하세요.

차트를 성공적으로 배포한 후 Azure Portal의 컨테이너에 대 한 Azure Monitor에서 하이브리드 Kubernetes 클러스터에 대 한 데이터를 검토할 수 있습니다.  

>[!NOTE]
>수집 대기 시간은 에이전트가 Azure Log Analytics 작업 영역에서 커밋하는 데 5 ~ 10 분 정도 소요 됩니다. 클러스터의 상태는 Azure Monitor에서 필요한 모든 모니터링 데이터를 사용할 수 있을 때까지 데이터를 표시 **하지** 않거나 **알 수 없는** 값을 표시 합니다.

## <a name="configure-proxy-endpoint"></a>프록시 끝점 구성

차트 버전 2.7.1 부터는 차트 매개 변수를 사용 하 여 프록시 끝점을 지정 하는 것이 지원 됩니다 `omsagent.proxy` . 이렇게 하면 프록시 서버를 통해 통신할 수 있습니다. 컨테이너 에이전트 및 Azure Monitor에 대 한 Azure Monitor 간의 통신은 HTTP 또는 HTTPS 프록시 서버가 될 수 있으며 익명 및 기본 인증 (사용자 이름/암호)이 모두 지원 됩니다.

프록시 구성 값의 구문은 다음과 같습니다. `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>프록시 서버에 인증이 필요 하지 않은 경우에도 유사 사용자 이름/암호를 지정 해야 합니다. 이는 사용자 이름 또는 암호일 수 있습니다.

|속성| Description |
|--------|-------------|
|프로토콜 | HTTP 또는 HTTPS |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|password | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버의 주소 또는 FQDN |
|포트 | 프록시 서버에 대 한 선택적 포트 번호 |

`omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

프로토콜을 **http** 로 지정 하는 경우에는 SSL/TLS 보안 연결을 사용 하 여 http 요청을 만듭니다. 프록시 서버는 SSL/TLS 프로토콜을 지원 해야 합니다.

## <a name="troubleshooting"></a>문제 해결

하이브리드 Kubernetes 클러스터에 대 한 모니터링을 사용 하도록 설정 하는 동안 오류가 발생 하는 경우 PowerShell 스크립트 [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) 을 복사 하 여 컴퓨터의 폴더에 저장 합니다. 이 스크립트는 발생 한 문제를 검색 하 고 해결 하는 데 도움이 됩니다. 검색 하 고 수정을 시도 하기 위해 설계 된 문제는 다음과 같습니다.

- 지정한 Log Analytics 작업 영역이 올바릅니다.
- Log Analytics 작업 영역은 컨테이너 솔루션에 대 한 Azure Monitor를 사용 하 여 구성 됩니다. 그렇지 않은 경우 작업 영역을 구성 합니다.
- OmsAgent replicaset pod가 실행 중입니다.
- OmsAgent daemonset pod가 실행 중입니다.
- OmsAgent Health service가 실행 되 고 있습니다.
- 컨테이너 화 된 에이전트에 구성 된 Log Analytics 작업 영역 ID 및 키가 정보를 사용 하 여 구성 된 작업 영역과 일치 합니다.
- 모든 Linux 작업자 노드에 `kubernetes.io/role=agent` rs pod를 예약 하기 위한 레이블이 있는지 확인 합니다. 존재 하지 않는 경우 추가 합니다.
- 유효성을 검사 `cAdvisor secure port:10250` 하거나 `unsecure port: 10255` 클러스터의 모든 노드에서 열 수 있습니다.

Azure PowerShell를 사용 하 여 실행 하려면 스크립트가 포함 된 폴더에서 다음 명령을 사용 합니다.

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>다음 단계

모니터링을 사용 하 여 하이브리드 Kubernetes 클러스터의 상태 및 리소스 사용률을 수집 하 고 해당 작업에서 실행 되는 작업을 수집 합니다. 컨테이너에 Azure Monitor [를 사용 하는 방법을](container-insights-analyze.md) 알아봅니다.