---
title: 서비스 방화벽 규칙 구성
description: 선택한 공용 IP 주소 또는 주소 범위에서 Azure container registry에 대 한 액세스를 사용 하도록 설정 하는 IP 규칙을 구성 합니다.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984617"
---
# <a name="configure-public-ip-network-rules"></a>공용 IP 네트워크 규칙 구성

기본적으로 Azure container registry는 네트워크의 호스트에서 인터넷을 통한 연결을 허용 합니다. 이 문서에서는 특정 공용 IP 주소 또는 주소 범위 에서만 액세스할 수 있도록 컨테이너 레지스트리를 구성 하는 방법을 보여 줍니다. Azure CLI 및 Azure Portal를 사용 하는 것과 동일한 단계가 제공 됩니다.

IP 네트워크 규칙은 공용 레지스트리 끝점에서 구성 됩니다. [개인 링크로](container-registry-private-link.md) 구성 된 개인 끝점에는 IP 네트워크 규칙이 적용 되지 않습니다.

IP 액세스 규칙 구성은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조 하세요.

## <a name="access-from-selected-public-network---cli"></a>선택한 공용 네트워크에서 액세스-CLI

### <a name="change-default-network-access-to-registry"></a>레지스트리에 대 한 기본 네트워크 액세스 변경

선택한 공용 네트워크에 대 한 액세스를 제한 하려면 먼저 기본 작업을 변경 하 여 액세스를 거부 합니다. 다음 [az acr update][az-acr-update] 명령에서 레지스트리 이름을 대체 합니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

[Az acr network-rule add][az-acr-network-rule-add] 명령을 사용 하 여 공용 IP 주소 또는 범위에서 액세스를 허용 하는 네트워크 규칙을 레지스트리에 추가 합니다. 예를 들어 가상 네트워크에서 컨테이너 레지스트리의 이름과 VM의 공용 IP 주소를 대체 합니다.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 규칙을 추가한 후 규칙이 적용 되기까지 몇 분이 걸립니다.

## <a name="access-from-selected-public-network---portal"></a>선택한 공용 네트워크에서 액세스-포털

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. **설정**에서 **네트워킹**을 선택 합니다.
1. **공용 액세스** 탭에서 **선택한 네트워크**에서 공용 액세스를 허용 하도록 선택 합니다.
1. **방화벽**에서 가상 네트워크에 있는 VM의 공용 ip 주소와 같은 공용 ip 주소를 입력 합니다. 또는 VM의 IP 주소를 포함 하는 CIDR 표기법으로 주소 범위를 입력 합니다.
1. **저장**을 선택합니다.

![컨테이너 레지스트리에 대 한 방화벽 규칙 구성][acr-access-selected-networks]

> [!NOTE]
> 규칙을 추가한 후 규칙이 적용 되기까지 몇 분이 걸립니다.

> [!TIP]
> 필요에 따라 로컬 클라이언트 컴퓨터 또는 IP 주소 범위에서 레지스트리 액세스를 사용 하도록 설정 합니다. 이 액세스를 허용 하려면 컴퓨터의 공용 IPv4 주소가 필요 합니다. 인터넷 브라우저에서 "내 IP 주소는 무엇입니까"를 검색 하 여이 주소를 찾을 수 있습니다. 포털의 **네트워킹** 페이지에서 방화벽 설정을 구성 하는 경우에도 현재 클라이언트 IPv4 주소가 자동으로 표시 됩니다.

## <a name="disable-public-network-access"></a>공용 네트워크 액세스 사용 안 함

[개인 링크](container-registry-private-link.md)를 사용 하 여 트래픽을 가상 네트워크로 제한 하려면 레지스트리에서 공용 끝점을 사용 하지 않도록 설정 합니다. 공용 끝점을 사용 하지 않도록 설정 하면 모든 방화벽 구성이 재정의 됩니다.

### <a name="disable-public-access---portal"></a>공용 액세스 사용 안 함-포털

1. 포털에서 컨테이너 레지스트리로 이동 하 고 **설정 > 네트워킹**을 선택 합니다.
1. 공용 **액세스** 탭의 **공용 액세스 허용**에서 **사용 안 함**을 선택 합니다. 그런 다음 **저장**을 선택합니다.

![공용 액세스 사용 안 함][acr-access-disabled]

## <a name="restore-default-registry-access"></a>기본 레지스트리 액세스 복원

기본적으로 액세스를 허용 하도록 레지스트리를 복원 하려면 기본 작업을 업데이트 합니다. 

### <a name="restore-default-registry-access---portal"></a>기본 레지스트리 액세스 복원-포털

1. 포털에서 컨테이너 레지스트리로 이동 하 고 **설정 > 네트워킹**을 선택 합니다.
1. **방화벽**에서 각 주소 범위를 선택 하 고 삭제 아이콘을 선택 합니다.
1. 공용 **액세스** 탭의 **공용 액세스 허용**에서 **모든 네트워크**를 선택 합니다. 그런 다음 **저장**을 선택합니다.

![모든 네트워크에서 공용 액세스][acr-access-all-networks]

## <a name="next-steps"></a>다음 단계

* 가상 네트워크의 개인 끝점을 사용 하 여 레지스트리에 대 한 액세스를 제한 하려면 [azure container registry에 대 한 Azure 개인 링크 구성](container-registry-private-link.md)을 참조 하세요.
* 클라이언트 방화벽 뒤에서 레지스트리 액세스 규칙을 설정 해야 하는 경우 [방화벽 뒤에 있는 Azure container registry에 액세스 하도록 규칙 구성](container-registry-firewall-access-rules.md)을 참조 하세요.

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
