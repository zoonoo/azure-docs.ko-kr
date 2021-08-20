---
title: '빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결'
description: 이 빠른 시작에서는 Azure Arc 지원 Kubernetes 클러스터를 연결하는 방법을 알아봅니다.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 06/30/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli, devx-track-azurepowershell
keywords: Kubernetes, Arc, Azure, 클러스터
ms.openlocfilehash: 1464f7f2c9d38b859823ab99e52499642fa4af4b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595843"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결

이 빠른 시작에서는 Azure Arc 지원 Kubernetes의 이점을 활용하고 기존 Kubernetes 클러스터를 Azure Arc에 연결하는 방법을 알아봅니다. 클러스터를 Azure Arc에 연결하는 개념은 [Azure Arc 지원 Kubernetes 에이전트 아키텍처 문서](./conceptual-agent-architecture.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

* 실행 중인 Kubernetes 클러스터. 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    * [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    * [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터
    * OpenShift 클러스터를 Azure Arc에 연결하려면 `az connectedk8s connect`를 실행하기 전에 클러스터에서 다음 명령을 한 번만 실행해야 합니다.

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 클러스터에는 운영 체제 및 아키텍처 유형 `linux/amd64`의 노드가 하나 이상 있어야 합니다. `linux/arm64` 노드만 있는 클러스터는 아직 지원되지 않습니다.

* 클러스터를 가리키는 `kubeconfig` 파일 및 컨텍스트입니다.
* Azure Arc 지원 Kubernetes 리소스 유형(`Microsoft.Kubernetes/connectedClusters`)에 대한 '읽기'및 '쓰기' 사용 권한입니다.

* [Helm 3의 최신 릴리스](https://helm.sh/docs/intro/install)를 설치합니다.

* 버전 >= 2.16.0으로 [Azure CLI 설치 또는 업그레이드](/cli/azure/install-azure-cli)
* `connectedk8s` Azure CLI 확장 버전 >= 1.0.0을 설치합니다.

  ```console
  az extension add --name connectedk8s
  ```
>[!NOTE]
> 클러스터의 [**사용자 지정 위치**](./custom-locations.md)의 경우 미국 동부 또는 서유럽 지역을 사용합니다. 다른 모든 Azure Arc 지원 Kubernetes 기능의 경우 [이 목록에서 지역을 선택합니다](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> **Az.ConnectedKubernetes** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다.

```azurepowershell-interactive
Install-Module -Name Az.ConnectedKubernetes
```

* 실행 중인 Kubernetes 클러스터. 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    * [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    * [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터
    * OpenShift 클러스터를 Azure Arc에 연결하려면 `New-AzConnectedKubernetes`를 실행하기 전에 클러스터에서 다음 명령을 한 번만 실행해야 합니다.

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 클러스터에는 운영 체제 및 아키텍처 유형 `linux/amd64`의 노드가 하나 이상 있어야 합니다. `linux/arm64` 노드만 있는 클러스터는 아직 지원되지 않습니다.

* 클러스터를 가리키는 `kubeconfig` 파일 및 컨텍스트입니다.
* Azure Arc 지원 Kubernetes 리소스 유형(`Microsoft.Kubernetes/connectedClusters`)에 대한 '읽기'및 '쓰기' 사용 권한입니다.

* [Helm 3의 최신 릴리스](https://helm.sh/docs/intro/install)를 설치합니다.

* [Azure PowerShell](/powershell/azure/install-az-ps) 버전 5.9.0 이상.

---

>[!NOTE]
> 클러스터의 [**사용자 지정 위치**](./custom-locations.md)의 경우 미국 동부 또는 서유럽 지역을 사용합니다. 다른 모든 Azure Arc 지원 Kubernetes 기능의 경우 [이 목록에서 지역을 선택합니다](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="meet-network-requirements"></a>네트워크 요구 사항 충족

> [!IMPORTANT]
> Azure Arc 에이전트가 작동하려면 다음 프로토콜/포트/아웃바운드 URL이 필요합니다.
> * 포트 443의 TCP: `https://:443`

| 엔드포인트(DNS) | Description |
| ----------------- | ------------- |
| `https://management.azure.com`(Azure 클라우드의 경우), `https://management.usgovcloudapi.net`(Azure 미국 정부의 경우) | 에이전트가 Azure에 연결하고 클러스터를 등록하는 데 필요합니다. |
| `https://<region>.dp.kubernetesconfiguration.azure.com`(Azure 클라우드의 경우), `https://<region>.dp.kubernetesconfiguration.azure.us`(Azure 미국 정부의 경우) | 에이전트가 상태를 푸시하고 구성 정보를 가져오기 위한 데이터 평면 엔드포인트. |
| `https://login.microsoftonline.com`(Azure 클라우드의 경우), `https://login.microsoftonline.us`(Azure 미국 정부의 경우) | Azure Resource Manager 토큰을 가져오고 업데이트하는 데 필요합니다. |
| `https://mcr.microsoft.com` | Azure Arc 에이전트의 컨테이너 이미지를 끌어오는 데 필요합니다.                                                                  |
| `https://gbl.his.arc.azure.com` |  시스템 할당 MSI(Managed Service Identity) 인증서를 끌어오기 위한 지역 엔드포인트를 가져오는 데 필요합니다. |
| `https://<region-code>.his.arc.azure.com`(Azure 클라우드의 경우), `https://usgv.his.arc.azure.us`(Azure 미국 정부의 경우) |  시스템 할당 MSI(관리 서비스 ID) 인증서를 가져오는 데 필요합니다. Azure 클라우드 지역에 대한 `<region-code>` 매핑: `eus`(미국 동부), `weu`(서유럽), `wcus`(미국 중서부), `scus`(미국 중남부), `sea`(동남 아시아), `uks`(영국 남부), `wus2`(미국 서부 2), `ae`(오스트레일리아 동부), `eus2`(미국 동부 2), `ne`(북유럽), `fc`(프랑스 중부). |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Azure Arc 지원 Kubernetes에 공급자 등록

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 다음 명령을 입력합니다.
    ```console
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    ```console
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. 다음 명령을 입력합니다.
    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    ```azurepowershell-interactive
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

---

## <a name="2-create-a-resource-group"></a>2. 리소스 그룹 만들기

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az group create --name AzureArcTest --location EastUS --output table
```

출력:
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

출력:
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 기존 Kubernetes 클러스터 연결

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

출력:
<pre>
Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
</pre>

> [!TIP]
> 위치 매개 변수가 지정되지 않은 상태에서 위 명령을 사용하면 동일한 위치에서 Azure Arc 지원 Kubernetes 리소스가 리소스 그룹으로 만들어집니다. 다른 위치에서 Azure Arc 지원 Kubernetes 리소스를 만들려면 `az connectedk8s connect` 명령을 실행할 때 `--location <region>` 또는 `-l <region>`을 지정합니다.

> [!NOTE]
> 서비스 주체를 사용하여 Azure CLI에 로그인한 경우 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하려면 [추가 매개 변수](troubleshooting.md#enable-custom-locations-using-service-principal)를 설정해야 합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

출력:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4-verify-cluster-connection"></a>4. 클러스터 연결 확인

다음 명령을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```console
az connectedk8s list --resource-group AzureArcTest --output table
```

출력:
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

출력:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> 클러스터를 온보드한 후 Azure Portal의 Azure Arc 지원 Kubernetes 리소스의 개요 페이지에 클러스터 메타데이터(클러스터 버전, 에이전트 버전, 노드 수 등)가 표시되는 데 약 5~10분이 걸립니다.

## <a name="5-connect-using-an-outbound-proxy-server"></a>5. 아웃바운드 프록시 서버를 사용하여 연결

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

클러스터가 아웃바운드 프록시 서버 뒤에 있는 경우, Azure CLI 및 Azure Arc 지원 Kubernetes 에이전트는 아웃바운드 프록시 서버를 통해 요청을 라우팅해야 합니다.

1. 아웃바운드 프록시 서버를 사용하기 위해 Azure CLI에 필요한 환경 변수를 설정합니다.

    * Bash를 사용하는 경우 적절한 값을 사용하여 다음 명령을 실행합니다.

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * PowerShell을 사용하는 경우 적절한 값을 사용하여 다음 명령을 실행합니다.

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. 프록시 매개 변수를 지정하여 connect 명령을 실행합니다.

    ```console
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * 에이전트에 대한 클러스터 내 통신이 끊어지지 않도록 `--proxy-skip-range` 아래에 `excludedCIDR`을 지정합니다.
> * 대부분의 아웃바운드 프록시 환경에서 `--proxy-http`, `--proxy-https` 및 `--proxy-skip-range`가 필요합니다. `--proxy-cert`는 프록시에서 예상되는 신뢰할 수 있는 인증서를 에이전트 Pod의 신뢰할 수 있는 인증서 저장소에 삽입해야 하는 경우에 *만* 필요합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

클러스터가 아웃바운드 프록시 서버 뒤에 있는 경우, Azure PowerShell 및 Azure Arc 지원 Kubernetes 에이전트는 아웃바운드 프록시 서버를 통해 요청을 라우팅해야 합니다.

1. 아웃바운드 프록시 서버를 사용하기 위해 Azure PowerShell에 필요한 환경 변수를 설정합니다.

    * 적절한 값으로 다음 명령을 실행합니다.

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. 프록시 매개 변수를 지정하여 connect 명령을 실행합니다.

    ```azurepowershell-interactive
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Kubernetes용 Azure Arc 에이전트 보기

Azure Arc가 지원되는 Kubernetes는 `azure-arc` 네임스페이스에 소수의 연산자를 배포합니다.

1. 이러한 배포 및 Pod는 다음을 사용하여 볼 수 있습니다.

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. 모든 Pod가 `Running` 상태인지 확인합니다.

    출력:
    <pre>

    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    </pre>

## <a name="7-clean-up-resources"></a>7. 리소스 정리

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 명령을 사용하여 Azure Arc 지원 Kubernetes 리소스, 모든 관련 구성 리소스 *그리고* Azure CLI를 사용하여 클러스터에서 실행 중인 모든 에이전트를 삭제할 수 있습니다.

```console
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> Azure Portal을 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하면 관련된 모든 구성 리소스가 제거되지만 클러스터에서 실행 중인 에이전트는 *제거되지 않습니다*. 따라서 Azure Portal 대신 `az connectedk8s delete`를 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하는 것이 가장 좋습니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 명령을 사용하여 Azure Arc 지원 Kubernetes 리소스, 모든 관련 구성 리소스, *그리고* Azure PowerShell을 사용하여 클러스터에서 실행 중인 모든 에이전트를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> Azure Portal을 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하면 관련된 모든 구성 리소스가 제거되지만 클러스터에서 실행 중인 에이전트는 *제거되지 않습니다*. 따라서 Azure Portal 대신 `Remove-AzConnectedKubernetes`를 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하는 것이 가장 좋습니다.

---

## <a name="next-steps"></a>다음 단계

GitOps를 사용하여 연결된 Kubernetes 클러스터에 구성을 배포하는 방법을 알아보려면 다음 문서로 이동합니다.
> [!div class="nextstepaction"]
> [GitOps를 사용하여 구성 배포](tutorial-use-gitops-connected-cluster.md)
