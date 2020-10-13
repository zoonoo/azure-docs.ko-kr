---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Arc enabled Kubernetes 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 Azure Arc 사용 Kubernetes 클러스터의 컨테이너에 대 한 Azure Monitor를 사용 하 여 모니터링을 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 79a534e4f37fb0154115e43402f031752a603ccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620293"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 사용 Kubernetes 클러스터 모니터링 사용

컨테이너에 대 한 Azure Monitor는 AKS (Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대 한 풍부한 모니터링 환경을 제공 합니다. 이 문서에서는 azure Arc에서 사용 하도록 설정 된 Azure 외부에서 호스트 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정 하 여 비슷한 모니터링 환경을 구현 하는 방법을 설명 합니다.

PowerShell 또는 Bash 스크립트를 사용 하 여 Kubernetes의 기존 배포 하나 이상에서 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정할 수 있습니다.

## <a name="supported-configurations"></a>지원되는 구성

컨테이너에 대 한 Azure Monitor는 [개요](container-insights-overview.md) 문서에 설명 된 대로 다음 기능을 제외 하 고 Azure Arc enabled Kubernetes (미리 보기) 모니터링을 지원 합니다.

- 라이브 데이터 (미리 보기)

컨테이너의 Azure Monitor는 다음이 공식적으로 지원 됩니다.

- Kubernetes 및 지원 정책의 버전은 [지원 되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일 합니다.

- 다음 컨테이너 런타임이 지원 됩니다. Docker, Moby 및 CRI compatible runtime (CRI 및 ContainerD).

- 지원 되는 마스터 및 작업자 노드에 대 한 Linux OS 릴리스는 Ubuntu (18.04 LTS 및 16.04 LTS)입니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 항목이 있는지 확인하십시오.

- Log Analytics 작업 영역.

    컨테이너 Azure Monitor는 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열 된 지역에서 Log Analytics 작업 영역을 지원 합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)또는 [Azure Portal](../learn/quick-create-workspace.md)를 통해 만들 수 있습니다.

- 컨테이너에 대 한 Azure Monitor의 기능을 사용 하도록 설정 하 고 액세스 하려면 최소한 Azure 구독에서 Azure *참가자* 역할의 멤버 여야 하 고 컨테이너에 대 한 Azure Monitor로 구성 된 Log Analytics 작업 영역의 구성원 인 [*Log Analytics 참가자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할의 구성원 이어야 합니다.

- 사용자는 Azure Arc 클러스터 리소스에서 [참가자](../../role-based-access-control/built-in-roles.md#contributor) 역할의 구성원입니다.

- 모니터링 데이터를 보려면 컨테이너에 대해 Azure Monitor 구성 된 Log Analytics 작업 영역에 대 한 [*Log Analytics 읽기 권한자*](../platform/manage-access.md#manage-access-using-azure-permissions) 역할 권한의 멤버입니다.

- 지정 된 Kubernetes 클러스터에 대 한 컨테이너 차트의 Azure Monitor를 등록 하는 [클라이언트](https://helm.sh/docs/using_helm/) 를 작성 합니다.

- 다음 프록시 및 방화벽 구성 정보는 Linux 용 Log Analytics 에이전트의 컨테이너 화 된 버전이 Azure Monitor와 통신 하는 데 필요 합니다.

    |에이전트 리소스|포트 |
    |------|---------|
    |`*.ods.opinsights.azure.com` |포트 443 |
    |`*.oms.opinsights.azure.com` |포트 443 |
    |`*.dc.services.visualstudio.com` |포트 443 |

- 컨테이너 화 된 에이전트는 `cAdvisor secure port: 10250` `unsecure port :10255` 성능 메트릭을 수집 하기 위해 클러스터의 모든 노드에서 Kubelet 또는를 열어야 합니다. `secure port: 10250`아직 구성 되지 않은 경우 Kubelet의 cAdvisor에서 구성 하는 것이 좋습니다.

- 컨테이너 화 된 에이전트는 인벤토리 데이터를 수집 하기 위해 클러스터 내의 Kubernetes API 서비스와 통신 하기 위해 컨테이너에서 다음 환경 변수를 지정 해야 합니다 `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Arc 사용 Kubernetes 클러스터 모니터링에 대해 지원 되는 최소 에이전트 버전은 ciprod04162020 이상입니다.

- Powershell [Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) 는 powershell 스크립팅된 메서드를 사용 하 여 모니터링을 사용 하도록 설정 하는 경우에 필요 합니다.

- Bash 스크립팅된 메서드를 사용 하 여 모니터링을 사용 하도록 설정 하는 경우 [bash 버전 4](https://www.gnu.org/software/bash/) 가 필요 합니다.

## <a name="identify-workspace-resource-id"></a>작업 영역 리소스 ID 식별

이전에 다운로드 한 PowerShell 또는 bash 스크립트를 사용 하 여 클러스터의 모니터링을 사용 하도록 설정 하 고 기존 Log Analytics 작업 영역에 통합 하려면 다음 단계를 수행 하 여 Log Analytics 작업 영역의 전체 리소스 ID를 먼저 확인 합니다. `workspaceResourceId`지정 된 작업 영역에 대해 모니터링 추가 기능을 사용 하도록 설정 하는 명령을 실행할 때 매개 변수에 필요 합니다. 지정할 작업 영역이 없는 경우 `workspaceResourceId` 매개 변수 포함을 건너뛰고 스크립트에서 새 작업 영역을 만들도록 할 수 있습니다.

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

    **SubscriptionId**의 값을 복사 합니다.

2. 다음 명령을 사용 하 여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환 합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예에서는 구독의 작업 영역 목록을 기본 JSON 형식으로 표시 합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID**로 복사 합니다.

## <a name="enable-monitoring-using-powershell"></a>PowerShell을 사용 하 여 모니터링 사용

1. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 구성 하는 로컬 폴더에 스크립트를 다운로드 하 고 저장 합니다.

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. `$azureArcClusterResourceId`에 해당 하는 값을 설정 하 `subscriptionId` `resourceGroupName` 고 `clusterName` Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 변수를 구성 합니다.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`명령을 실행 하 여 클러스터의 **kube 컨텍스트로** 변수를 구성 합니다 `kubectl config get-contexts` . 현재 컨텍스트를 사용 하려면 값을로 설정 `""` 합니다.

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 기존 Azure Monitor Log Analytics 작업 영역을 사용 하려면 `$logAnalyticsWorkspaceResourceId` 작업 영역의 리소스 ID를 나타내는 해당 값을 사용 하 여 변수를 구성 합니다. 그렇지 않으면 변수를로 설정 하 `""` 고, 해당 지역에 아직 없는 경우 스크립트는 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만듭니다. 만든 기본 작업 영역은 *defaultworkspace \<SubscriptionID> - \<Region> *의 형식과 유사 합니다.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc 사용 Kubernetes 클러스터가 프록시 서버를 통해 통신 하는 경우 `$proxyEndpoint` 프록시 서버의 URL을 사용 하 여 변수를 구성 합니다. 클러스터가 프록시 서버를 통해 통신 하지 않는 경우이 값을로 설정할 수 있습니다 `""` .  자세한 내용은이 문서의 뒷부분에 있는 [프록시 끝점 구성](#configure-proxy-endpoint) 을 참조 하세요.

6. 다음 명령을 실행 하 여 모니터링을 사용 하도록 설정 합니다.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="using-service-principal"></a>서비스 주체 사용
스크립트 *enable-monitoring.ps1* 는 대화형 장치 로그인을 사용 합니다. 비 대화형 로그인을 선호 하는 경우 기존 서비스 주체를 사용 하거나 [필수 구성 요소](#prerequisites)에 설명 된 대로 필요한 권한이 있는 새 서비스 주체를 만들 수 있습니다. 서비스 주체를 사용 하려면 스크립트를 *enable-monitoring.ps1* 하는 데 사용할 서비스 주체의 값을 사용 하 여 $servicePrincipalClientId, $servicePrincipalClientSecret 및 $tenantId 매개 변수를 전달 해야 합니다.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

아래 역할 할당은 Arc K8s 연결 된 클러스터 리소스와 다른 Azure 구독에서 기존 Log Analytics 작업 영역을 사용 하는 경우에만 적용할 수 있습니다.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

예를 들면 다음과 같습니다.

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Bash 스크립트를 사용 하도록 설정

제공 된 bash 스크립트를 사용 하 여 모니터링을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 다음 명령을 사용 하 여 모니터링 추가 기능을 사용 하 여 클러스터를 구성 하는 로컬 폴더에 스크립트를 다운로드 하 고 저장 합니다.

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. `azureArcClusterResourceId`에 해당 하는 값을 설정 하 `subscriptionId` `resourceGroupName` 고 `clusterName` Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 변수를 구성 합니다.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`명령을 실행 하 여 클러스터의 **kube 컨텍스트로** 변수를 구성 합니다 `kubectl config get-contexts` . 현재 컨텍스트를 사용 하려면 값을로 설정 `""` 합니다.

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 기존 Azure Monitor Log Analytics 작업 영역을 사용 하려면 `logAnalyticsWorkspaceResourceId` 작업 영역의 리소스 ID를 나타내는 해당 값을 사용 하 여 변수를 구성 합니다. 그렇지 않으면 변수를로 설정 하 `""` 고, 해당 지역에 아직 없는 경우 스크립트는 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만듭니다. 만든 기본 작업 영역은 *defaultworkspace \<SubscriptionID> - \<Region> *의 형식과 유사 합니다.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc 사용 Kubernetes 클러스터가 프록시 서버를 통해 통신 하는 경우 `proxyEndpoint` 프록시 서버의 URL을 사용 하 여 변수를 구성 합니다. 클러스터가 프록시 서버를 통해 통신 하지 않는 경우이 값을로 설정할 수 있습니다 `""` . 자세한 내용은이 문서의 뒷부분에 있는 [프록시 끝점 구성](#configure-proxy-endpoint) 을 참조 하세요.

6. 클러스터에 대 한 모니터링을 사용 하도록 설정 하려면 배포 시나리오에 따라 다양 한 명령이 제공 됩니다.

    다음 명령을 실행 하 여 기본 옵션으로 모니터링을 사용 하도록 설정 합니다. 예를 들어 현재 kube 컨텍스트를 사용 하 고, 기본 Log Analytics 작업 영역을 만들고, 프록시 서버를 지정 하지 않아도 됩니다.

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    다음 명령을 실행 하 여 프록시 서버를 지정 하지 않고 기본 Log Analytics 작업 영역을 만듭니다.

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    다음 명령을 실행 하 여 프록시 서버를 지정 하지 않고 기존 Log Analytics 작업 영역을 사용 합니다.

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    다음 명령을 실행 하 여 기존 Log Analytics 작업 영역을 사용 하 고 프록시 서버를 지정 합니다.

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="using-service-principal"></a>서비스 주체 사용
Bash 스크립트 *enable-monitoring.sh* 는 대화형 장치 로그인을 사용 합니다. 비 대화형 로그인을 선호 하는 경우 기존 서비스 주체를 사용 하거나 [필수 구성 요소](#prerequisites)에 설명 된 대로 필요한 권한이 있는 새 서비스 주체를 만들 수 있습니다. 서비스 주체를 사용 하려면 bash 스크립트를 *enable-monitoring.sh* 하는 데 사용할 서비스 주체의--클라이언트 id,--클라이언트 암호 및--테 넌 트 id 값을 전달 해야 합니다.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

아래 역할 할당은 Arc K8s 연결 된 클러스터 리소스와 다른 Azure 구독에서 기존 Log Analytics 작업 영역을 사용 하는 경우에만 적용할 수 있습니다.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

예를 들면 다음과 같습니다.

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>프록시 끝점 구성

컨테이너의 Azure Monitor에 대 한 컨테이너 화 된 agent를 사용 하 여 프록시 서버를 통해 통신할 수 있도록 프록시 끝점을 구성할 수 있습니다. 컨테이너 화 된 에이전트와 Azure Monitor 간의 통신은 HTTP 또는 HTTPS 프록시 서버가 될 수 있으며 익명 및 기본 인증 (사용자 이름/암호)이 모두 지원 됩니다.

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

`http://user01:password@proxy01.contoso.com:3128`

프로토콜을 **http**로 지정 하는 경우에는 SSL/TLS 보안 연결을 사용 하 여 http 요청을 만듭니다. 프록시 서버는 SSL/TLS 프로토콜을 지원 해야 합니다.

### <a name="configure-using-powershell"></a>PowerShell을 사용한 구성

프록시 서버에 대 한 사용자 이름 및 암호, IP 주소 또는 FQDN 및 포트 번호를 지정 합니다. 예를 들면 다음과 같습니다.

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Bash를 사용 하 여 구성

프록시 서버에 대 한 사용자 이름 및 암호, IP 주소 또는 FQDN 및 포트 번호를 지정 합니다. 예를 들면 다음과 같습니다.

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용 하 여 Arc 사용 Kubernetes 클러스터 및 작업에서 실행 되는 작업의 상태 및 리소스 사용률을 수집 하려면 컨테이너에 Azure Monitor [를 사용 하는 방법을](container-insights-analyze.md) 알아보세요.

- 기본적으로 컨테이너 화 된 에이전트는 kube를 제외한 모든 네임 스페이스에서 실행 중인 모든 컨테이너의 stdout/stderr 컨테이너 로그를 수집 합니다. 특정 네임 스페이스 또는 네임 스페이스에 특정 한 컨테이너 로그 컬렉션을 구성 하려면 [컨테이너 Insights 에이전트 구성](container-insights-agent-config.md) 을 검토 하 여 원하는 데이터 수집 설정을 configmap 구성 파일에 구성 합니다.

- 클러스터에서 프로메테우스 메트릭을 스크랩 하 고 분석 하려면 [프로메테우스 메트릭 구성](container-insights-prometheus-integration.md) 을 검토 하세요.

- 컨테이너에 대 한 Azure Monitor를 사용 하 여 Arc 사용 가능 Kubernetes 클러스터 모니터링을 중지 하는 방법을 알아보려면 [하이브리드 클러스터 모니터링을 중지 하는 방법](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)을 참조 하세요.
