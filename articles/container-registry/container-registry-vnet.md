---
title: 서비스 끝점을 사용 하 여 액세스 제한
description: Azure 가상 네트워크의 서비스 끝점을 사용 하 여 Azure container registry에 대 한 액세스 제한
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: da5ab67d6658d8760565353e2a690c53d862d0ed
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982584"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Azure 가상 네트워크의 서비스 끝점을 사용 하 여 컨테이너 레지스트리에 대 한 액세스 제한

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 는 azure 및 온-프레미스 리소스에 대 한 안전한 개인 네트워킹을 제공 합니다. [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 을 사용 하면 컨테이너 레지스트리의 공용 IP 주소를 가상 네트워크에만 보안을 유지할 수 있습니다. 이 끝점은 Azure 백본 네트워크를 통해 리소스에 대 한 최적의 경로를 트래픽을 제공 합니다. 가상 네트워크 및 서브넷의 ID 또한 각 요청과 함께 전송됩니다.

이 문서에서는 가상 네트워크에서 컨테이너 레지스트리 서비스 끝점 (미리 보기)을 구성 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이제 Azure Container Registry는 [Azure 개인 링크](container-registry-private-link.md)를 지원 하 여 가상 네트워크의 개인 끝점을 레지스트리에 배치할 수 있도록 합니다. 전용 끝점은 개인 IP 주소를 사용 하 여 가상 네트워크 내에서 액세스할 수 있습니다. 대부분의 네트워크 시나리오에서는 서비스 끝점 대신 개인 끝점을 사용 하는 것이 좋습니다.

레지스트리 서비스 끝점 구성은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조 하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* Azure Container Registry에 대 한 서비스 끝점의 향후 개발은 현재 계획 되지 않았습니다. 대신 [개인 끝점](container-registry-private-link.md) 을 사용 하는 것이 좋습니다.
* Azure Portal를 사용 하 여 레지스트리에 대 한 서비스 끝점을 구성할 수 없습니다.
* 서비스 끝점을 사용 하 여 컨테이너 레지스트리에 액세스 하기 위해 [Azure Kubernetes 서비스](../aks/intro-kubernetes.md) 클러스터 또는 azure [virtual machines](../virtual-machines/linux/overview.md) 만 호스트로 사용할 수 있습니다. *Azure Container Instances를 비롯 한 다른 Azure 서비스는 지원 되지 않습니다.*
* 각 레지스트리는 최대 100의 네트워크 액세스 규칙을 지원 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서의 Azure CLI 단계를 사용 하려면 Azure CLI 버전 2.0.58 이상이 필요 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

* 컨테이너 레지스트리가 아직 없는 경우 하나 (프리미엄 SKU 필요)를 만들고 Docker 허브에서와 `hello-world` 같은 샘플 이미지를 푸시합니다. 예를 들어 [Azure Portal][quickstart-portal] 또는 [Azure CLI][quickstart-cli] 를 사용 하 여 레지스트리를 만듭니다. 

* 다른 Azure 구독에서 서비스 끝점을 사용 하 여 레지스트리 액세스를 제한 하려면 해당 구독에 Azure Container Registry에 대 한 리소스 공급자를 등록 합니다. 다음은 그 예입니다. 

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>레지스트리에 대 한 네트워크 액세스 구성

이 섹션에서는 Azure 가상 네트워크의 서브넷에서 액세스할 수 있도록 컨테이너 레지스트리를 구성 합니다. Azure CLI 및 Azure Portal를 사용 하는 것과 동일한 단계가 제공 됩니다.

### <a name="allow-access-from-a-virtual-network---cli"></a>가상 네트워크에서 액세스 허용-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>서브넷에 서비스 끝점 추가

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 컴퓨터의 이름을 기반으로 합니다. 예를 들어 가상 컴퓨터 *Mydockervm*의 이름을 지정 하는 경우 기본 가상 네트워크 이름은 Mydockervmvnet 이며 서브넷은 *Mydockervm*입니다. *myDockerVMVNET* Azure Portal에서 또는 [az network vnet list][az-network-vnet-list] 명령을 사용 하 여이를 확인 합니다.

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

출력:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

[Az network vnet subnet update][az-network-vnet-subnet-update] 명령을 사용 하 여 **microsoft.containerregistry** 서비스 끝점을 서브넷에 추가 합니다. 다음 명령에서 가상 네트워크 및 서브넷의 이름을 대체 합니다.

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

[Az network vnet subnet show][az-network-vnet-subnet-show] 명령을 사용 하 여 서브넷의 리소스 ID를 검색 합니다. 이후 단계에서 네트워크 액세스 규칙을 구성 하는 데 필요 합니다.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

출력:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>레지스트리에 대 한 기본 네트워크 액세스 변경

기본적으로 Azure container registry를 사용 하면 모든 네트워크의 호스트에서 연결할 수 있습니다. 선택한 네트워크에 대 한 액세스를 제한 하려면 액세스 거부로 기본 작업을 변경 합니다. 다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 대체 합니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

[Az acr network-rule add][az-acr-network-rule-add] 명령을 사용 하 여 VM의 서브넷에서의 액세스를 허용 하는 네트워크 규칙을 레지스트리에 추가 합니다. 다음 명령에서 컨테이너 레지스트리의 이름 및 서브넷의 리소스 ID를 대체 합니다. 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>레지스트리에 대 한 액세스 확인

구성을 업데이트할 때까지 몇 분 정도 기다린 후 VM이 컨테이너 레지스트리에 액세스할 수 있는지 확인 합니다. VM에 대 한 SSH 연결을 설정 하 고 [az acr login][az-acr-login] 명령을 실행 하 여 레지스트리에 로그인 합니다. 

```bash
az acr login --name mycontainerregistry
```

실행 `docker pull` 등의 레지스트리 작업을 수행 하 여 레지스트리에서 샘플 이미지를 끌어올 수 있습니다. 레지스트리 로그인 서버 이름 (모두 소문자)을 접두사로 사용 하 여 레지스트리에 적절 한 이미지 및 태그 값을 대체 합니다.

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker가 성공적으로 이미지를 VM으로 끌어옵니다.

이 예제에서는 네트워크 액세스 규칙을 통해 개인 컨테이너 레지스트리에 액세스할 수 있는 방법을 보여 줍니다. 그러나 네트워크 액세스 규칙이 구성 되지 않은 로그인 호스트에서는 레지스트리에 액세스할 수 없습니다. `az acr login` 명령 또는 `docker login` 명령을 사용 하 여 다른 호스트에서 로그인을 시도 하는 경우 출력은 다음과 유사 합니다.

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>기본 레지스트리 액세스 복원

기본적으로 액세스를 허용 하도록 레지스트리를 복원 하려면 구성 된 네트워크 규칙을 모두 제거 합니다. 그런 다음 액세스를 허용 하도록 기본 작업을 설정 합니다. Azure CLI 및 Azure Portal를 사용 하는 것과 동일한 단계가 제공 됩니다.

### <a name="restore-default-registry-access---cli"></a>기본 레지스트리 액세스 복원-CLI

#### <a name="remove-network-rules"></a>네트워크 규칙 제거

레지스트리에 대해 구성 된 네트워크 규칙 목록을 보려면 다음 [az acr network-rule list][az-acr-network-rule-list] 명령을 실행 합니다.

```azurecli
az acr network-rule list --name mycontainerregistry 
```

구성 된 각 규칙에 대해 [az acr network-rule remove][az-acr-network-rule-remove] 명령을 실행 하 여 제거 합니다. 다음은 그 예입니다. 

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>액세스 허용

다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 대체 합니다.
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에서 모든 Azure 리소스를 만들고 더 이상 필요 하지 않은 경우에는 단일 [az group delete](/cli/azure/group) 명령을 사용 하 여 리소스를 선택적으로 삭제할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

포털에서 리소스를 정리 하려면 myResourceGroup 리소스 그룹으로 이동 합니다. 리소스 그룹이 로드 되 면 리소스 그룹 **삭제** 를 클릭 하 여 리소스 그룹 및 해당 그룹에 저장 된 리소스를 제거 합니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크의 개인 끝점을 사용 하 여 레지스트리에 대 한 액세스를 제한 하려면 [azure container registry에 대 한 Azure 개인 링크 구성](container-registry-private-link.md)을 참조 하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정 해야 하는 경우 [방화벽 뒤에 있는 Azure container registry에 액세스 하도록 규칙 구성](container-registry-firewall-access-rules.md)을 참조 하세요.


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
