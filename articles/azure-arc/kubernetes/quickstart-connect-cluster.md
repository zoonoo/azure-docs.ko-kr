---
title: '빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결'
description: 이 빠른 시작에서는 Azure Arc 지원 Kubernetes 클러스터를 연결하는 방법을 알아봅니다.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 05/25/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, 클러스터
ms.openlocfilehash: 6221de7a9cffe5ba4d2e1ed8cc8e47c372b6b578
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110373721"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>빠른 시작: Azure Arc에 기존 Kubernetes 클러스터 연결 

이 빠른 시작에서는 Azure Arc 지원 Kubernetes의 이점을 활용하고 기존 Kubernetes 클러스터를 Azure Arc에 연결하는 방법을 알아봅니다. 클러스터를 Azure Arc에 연결하는 개념은 [Azure Arc 지원 Kubernetes 에이전트 아키텍처 문서](./conceptual-agent-architecture.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* 실행 중인 Kubernetes 클러스터. 클러스터가 없는 경우 이러한 옵션 중 하나를 사용하여 클러스터를 만들 수 있습니다.
    * [Docker의 Kubernetes(KIND)](https://kind.sigs.k8s.io/)
    * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)용 Docker를 사용하여 Kubernetes 클러스터 만들기
    * [클러스터 API](https://cluster-api.sigs.k8s.io/user/quick-start.html)를 사용하는 자체 관리되는 Kubernetes 클러스터
    * OpenShift 클러스터를 Azure Arc에 연결하려면 `az connectedk8s connect`를 실행하기 전에 클러스터에서 다음 명령을 한 번만 실행해야 합니다.
        
        ```azurecli-interactive
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 클러스터에는 운영 체제 및 아키텍처 유형 `linux/amd64`의 노드가 하나 이상 있어야 합니다. `linux/arm64` 노드만 있는 클러스터는 아직 지원되지 않습니다.
    
* 클러스터를 가리키는 `kubeconfig` 파일 및 컨텍스트입니다.
* Azure Arc 지원 Kubernetes 리소스 유형(`Microsoft.Kubernetes/connectedClusters`)에 대한 '읽기'및 '쓰기' 사용 권한입니다.

* [Helm 3의 최신 릴리스](https://helm.sh/docs/intro/install)를 설치합니다.

* 버전 >= 2.16.0으로 [Azure CLI 설치 또는 업그레이드](/cli/azure/install-azure-cli)
* `connectedk8s` Azure CLI 확장 버전 >= 1.0.0을 설치합니다.
  
  ```azurecli-interactive
  az extension add --name connectedk8s
  ```
>[!NOTE]
> 클러스터의 [**사용자 지정 위치**](./custom-locations.md)의 경우 미국 동부 또는 서유럽 지역을 사용합니다. 다른 모든 Azure Arc 지원 Kubernetes 기능의 경우 [이 목록에서 지역을 선택합니다](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="meet-network-requirements"></a>네트워크 요구 사항 충족

> [!IMPORTANT]
> Azure Arc 에이전트가 작동하려면 다음 프로토콜/포트/아웃바운드 URL이 필요합니다.
> * 포트 443의 TCP: `https://:443`
> * 포트 9418의 TCP: `git://:9418`
  
| 엔드포인트(DNS) | 설명 |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | 에이전트가 Azure에 연결하고 클러스터를 등록하는 데 필요합니다.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | 에이전트가 상태를 푸시하고 구성 정보를 가져오기 위한 데이터 평면 엔드포인트.                                      |  
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager 토큰을 가져오고 업데이트하는 데 필요합니다.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Azure Arc 에이전트의 컨테이너 이미지를 끌어오는 데 필요합니다.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  시스템 할당 MSI(관리 서비스 ID) 인증서를 가져오는 데 필요합니다.                                                                  |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Azure Arc 지원 Kubernetes에 공급자 등록

1. 다음 명령을 입력합니다.
    ```azurecli-interactive
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    ```azurecli-interactive
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="2-create-a-resource-group"></a>2. 리소스 그룹 만들기

다음 명령을 실행합니다.  

```azurecli-interactive
az group create --name AzureArcTest --location EastUS --output table
```

출력:
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 기존 Kubernetes 클러스터 연결

다음 명령을 실행합니다.
```azurecli-interactive
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
> 서비스 주체를 사용하여 Azure CLI에 로그인한 경우, 클러스터를 Azure Arc에 연결할 때 사용자 지정 위치 기능을 사용하도록 설정하기 위해 서비스 주체에 대한 [추가 권한](troubleshooting.md#enable-custom-locations-using-service-principal)이 필요합니다.

## <a name="4-verify-cluster-connection"></a>4. 클러스터 연결 확인

다음 명령을 실행합니다.  

```azurecli-interactive
az connectedk8s list -resource-group AzureArcTest -output table
```

출력:
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

> [!NOTE]
> 클러스터를 온보드한 후 Azure Portal의 Azure Arc 지원 Kubernetes 리소스의 개요 페이지에 클러스터 메타데이터(클러스터 버전, 에이전트 버전, 노드 수 등)가 표시되는 데 약 5~10분이 걸립니다.

## <a name="5-connect-using-an-outbound-proxy-server"></a>5. 아웃바운드 프록시 서버를 사용하여 연결

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

    ```azurecli-interactive
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * 에이전트에 대한 클러스터 내 통신이 끊어지지 않도록 `--proxy-skip-range` 아래에 `excludedCIDR`을 지정합니다.
> * 대부분의 아웃바운드 프록시 환경에서 `--proxy-http`, `--proxy-https` 및 `--proxy-skip-range`가 필요합니다. `--proxy-cert`는 프록시에서 예상되는 신뢰할 수 있는 인증서를 에이전트 Pod의 신뢰할 수 있는 인증서 저장소에 삽입해야 하는 경우에 *만* 필요합니다.

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Kubernetes용 Azure Arc 에이전트 보기

Azure Arc가 지원되는 Kubernetes는 `azure-arc` 네임스페이스에 소수의 연산자를 배포합니다. 

1. 이러한 배포 및 Pod는 다음을 사용하여 볼 수 있습니다.

    ```azurecli-interactive
    kubectl -name azure-arc get deployments,pods
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

다음 명령을 사용하여 Azure Arc 지원 Kubernetes 리소스, 모든 관련 구성 리소스 *그리고* Azure CLI를 사용하여 클러스터에서 실행 중인 모든 에이전트를 삭제할 수 있습니다.

```azurecli-interactive
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Azure Portal을 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하면 관련된 모든 구성 리소스가 제거되지만 클러스터에서 실행 중인 에이전트는 *제거되지 않습니다*. 따라서 Azure Portal 대신 `az connectedk8s delete`를 사용하여 Azure Arc 지원 Kubernetes 리소스를 삭제하는 것이 가장 좋습니다.

## <a name="next-steps"></a>다음 단계

GitOps를 사용하여 연결된 Kubernetes 클러스터에 구성을 배포하는 방법을 알아보려면 다음 문서로 이동합니다.
> [!div class="nextstepaction"]
> [GitOps를 사용하여 구성 배포](tutorial-use-gitops-connected-cluster.md)
