---
title: 전용 풀을 사용 하 여 작업 실행-태스크
description: 레지스트리에 전용 계산 풀 (에이전트 풀)을 설정 하 여 Azure Container Registry 작업을 실행 합니다.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions
ms.openlocfilehash: 86c539c3b34ca0e54d65f15c4d9d01a99f9b31c6
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997372"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>전용 에이전트 풀에서 ACR 작업 실행

전용 계산 환경에서 [Azure Container Registry 작업][acr-tasks] 을 실행할 수 있도록 Azure에서 관리 하는 VM 풀 (*에이전트 풀*)을 설정 합니다. 레지스트리에서 풀을 하나 이상 구성한 후에는 서비스의 기본 계산 환경 대신 작업을 실행할 풀을 선택할 수 있습니다.

에이전트 풀은 다음을 제공 합니다.

- **Virtual network 지원** -Azure vnet에 에이전트 풀을 할당 하 여 컨테이너 레지스트리, 키 자격 증명 모음 또는 저장소와 같은 vnet의 리소스에 대 한 액세스를 제공 합니다.
- **필요에 따라 크기 조정** -계산 집약적 작업을 위해 에이전트 풀의 인스턴스 수를 늘리거나 0으로 크기를 조정 합니다. 청구는 풀 할당을 기반으로 합니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/container-registry/)을 참조 하세요.
- **유연한 옵션** -작업 워크 로드 요구 사항에 맞게 다양 한 [풀 계층](#pool-tiers) 및 크기 조정 옵션을 선택 합니다.
- **Azure 관리** -태스크 풀은 azure에서 패치 및 유지 관리 하 여 개별 vm을 유지 관리할 필요 없이 예약 된 할당을 제공 합니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry SKU][acr-tiers]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="preview-limitations"></a>미리 보기 제한 사항

- 작업 에이전트 풀은 현재 Linux 노드를 지원 합니다. Windows 노드는 현재 지원 되지 않습니다.
- 작업 에이전트 풀은 미국 서 부 2, 미국 중부, 미국 동부 2, 미국 동부, 미국 중부, 미국 정부 애리조나, 미국 정부, 미국 정부 버지니아 지역에서 미리 보기로 제공 됩니다.
- 각 레지스트리에 대해 기본 총 vCPU (코어) 할당량은 모든 표준 에이전트 풀의 경우 16이 고 격리 된 에이전트 풀의 경우 0입니다. 추가 할당에 대 한 [지원 요청][open-support-ticket] 을 엽니다.
- 현재 에이전트 풀에서 실행 되는 작업을 취소할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서의 Azure CLI 단계를 사용 하려면 Azure CLI 버전 2.3.1 이상이 필요 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요. 또는 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 실행합니다.
* 컨테이너 레지스트리가 아직 없는 경우 미리 보기 영역에서 [하나][create-reg-cli] (프리미엄 계층 필요)를 만듭니다.

## <a name="pool-tiers"></a>풀 계층

에이전트 풀 계층은 풀에서 인스턴스당 다음과 같은 리소스를 제공 합니다.

|서비스 계층    | Type  |  CPU  |메모리(GB)  |
|---------|---------|---------|---------|
|S1     |  표준    | 2       |    3     |
|S2     |  표준    | 4       |    8     |
|S3     |  표준    | 8       |   16     |
|I6     |  Isolated    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>작업 에이전트 풀 만들기 및 관리

### <a name="set-default-registry-optional"></a>기본 레지스트리 설정 (선택 사항)

다음에 나오는 Azure CLI 명령을 간소화 하려면 [az configure][az-configure] 명령을 실행 하 여 기본 레지스트리를 설정 합니다.

```azurecli
az configure --defaults acr=<registryName>
```

다음 예에서는 기본 레지스트리를 설정 했다고 가정 합니다. 그렇지 않은 경우 `--registry <registryName>` 각 명령에 매개 변수를 전달 `az acr` 합니다.

### <a name="create-agent-pool"></a>에이전트 풀 만들기

[Az acr agentpool create][az-acr-agentpool-create] 명령을 사용 하 여 에이전트 풀을 만듭니다. 다음 예에서는 계층 S2 풀 (4 개의 CPU/인스턴스)을 만듭니다. 기본적으로 풀은 인스턴스 1 개를 포함 합니다.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> 에이전트 풀 및 기타 풀 관리 작업을 만드는 작업을 완료 하는 데 몇 분 정도 걸립니다.

### <a name="scale-pool"></a>풀 크기 조정

[Az acr agentpool update][az-acr-agentpool-update] 명령을 사용 하 여 풀 크기를 확장 하거나 축소 합니다. 다음 예에서는 풀을 2 개의 인스턴스로 확장 합니다. 0 개의 인스턴스로 확장할 수 있습니다.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>가상 네트워크에서 풀 만들기

### <a name="add-firewall-rules"></a>방화벽 규칙 추가

작업 에이전트 풀을 사용 하려면 다음 Azure 서비스에 액세스 해야 합니다. 기존 네트워크 보안 그룹 또는 사용자 정의 경로에 다음 방화벽 규칙을 추가 해야 합니다.

| Direction | 프로토콜 | 원본         | 원본 포트 | 대상          | 대상 포트 | 사용됨    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureKeyVault        | 443       | Default |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | 스토리지              | 443       | Default |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | EventHub             | 443       | Default |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureActiveDirectory | 443       | Default |
| 아웃바운드  | TCP      | VirtualNetwork | 모두         | AzureMonitor         | 443       | Default |

> [!NOTE]
> 작업에 공용 인터넷의 추가 리소스가 필요한 경우 해당 규칙을 추가 합니다. 예를 들어 Docker 허브에서 기본 이미지를 가져오는 docker 빌드 작업을 실행 하거나 NuGet 패키지를 복원 하는 추가 규칙이 필요 합니다.

### <a name="create-pool-in-vnet"></a>VNet에서 풀 만들기

다음 예제에서는 네트워크 *mysubnet*의 *mysubnet* 서브넷에 에이전트 풀을 만듭니다.

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>에이전트 풀에서 작업 실행

다음 예에서는 태스크를 큐에 대기 시킬 때 에이전트 풀을 지정 하는 방법을 보여 줍니다.

> [!NOTE]
> ACR 태스크에서 에이전트 풀을 사용 하려면 풀에 인스턴스가 하나 이상 포함 되어 있는지 확인 합니다.
>

### <a name="quick-task"></a>빠른 작업

[Az acr build][az-acr-build] 명령을 사용 하 여 에이전트 풀에서 빠른 작업을 큐에 대기 하 고 `--agent-pool` 매개 변수를 전달 합니다.

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>자동으로 트리거된 작업

예를 들어 [az acr task create][az-acr-task-create]를 사용 하 여 에이전트 풀에서 예약 된 작업을 만들고 `--agent-pool` 매개 변수를 전달 합니다.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

작업 설정을 확인 하려면 [az acr task run][az-acr-task-run]을 실행 합니다.

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>쿼리 풀 상태

현재 에이전트 풀에서 예약 된 실행 수를 찾으려면 [az acr agentpool show][az-acr-agentpool-show]를 실행 합니다.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>다음 단계

클라우드의 컨테이너 이미지 빌드 및 유지 관리에 대 한 추가 예제를 보려면 [ACR 작업 자습서 시리즈](container-registry-tutorial-quick-task.md)를 확인 하세요.



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md