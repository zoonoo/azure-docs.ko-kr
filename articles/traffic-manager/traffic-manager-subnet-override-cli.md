---
title: Azure 트래픽 관리자 서브넷 Azure CLI를 사용하여 재정의 | 마이크로 소프트 문서
description: 이 문서에서는 트래픽 관리자 서브넷 재정의를 사용하여 미리 정의된 IP 범위를 통해 엔드포인트 매핑에 대한 최종 사용자 IP 주소를 기반으로 트래픽을 엔드포인트로 트래픽을 전달하는 트래픽 관리자 프로필의 라우팅 방법을 이해하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938465"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Azure CLI를 사용하여 트래픽 관리자 서브넷 재정의

트래픽 관리자 서브넷 재정의를 사용하면 프로필의 라우팅 방법을 변경할 수 있습니다.  재정의를 추가하면 최종 사용자의 IP 주소를 기반으로 트래픽이 미리 정의된 IP 범위와 끝점 매핑으로 전달됩니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방식

서브넷 재정의가 트래픽 관리자 프로필에 추가되면 트래픽 관리자는 먼저 최종 사용자의 IP 주소에 대한 서브넷 재정의가 있는지 확인합니다. 검색이 발견되면 사용자의 DNS 쿼리가 해당 끝점으로 이동됩니다.  매핑을 찾을 수 없는 경우 트래픽 관리자는 프로필의 원래 라우팅 방법으로 대체됩니다. 

IP 주소 범위는 CIDR 범위(예: 1.2.3.0/24) 또는 주소 범위(예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 끝점과 연결된 IP 범위는 해당 끝점에 고유해야 합니다. 서로 다른 끝점 간에 IP 범위가 겹치면 트래픽 관리자가 프로필을 거부합니다.

서브넷 재정의를 지원하는 라우팅 프로필에는 두 가지 유형이 있습니다.

* **지리적** - 트래픽 관리자가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 발견하면 끝점의 상태와 관계없이 쿼리를 끝점으로 라우팅합니다.
* **성능** - 트래픽 관리자가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 발견하면 정상인 경우에만 트래픽을 끝점으로 라우팅합니다.  서브넷 오버라이드 끝점이 정상적이지 않은 경우 트래픽 관리자는 성능 라우팅 휴리스틱으로 돌아갑니다.

## <a name="create-a-traffic-manager-subnet-override"></a>트래픽 관리자 서브넷 재정의 만들기

트래픽 관리자 서브넷 재정의를 만들려면 Azure CLI를 사용하여 재정의에 대한 서브넷을 트래픽 관리자 끝점에 추가할 수 있습니다.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하십시오.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>서브넷 재정의를 사용하여 트래픽 관리자 끝점을 업데이트합니다.
Azure CLI를 사용하여 az [네트워크 트래픽 관리자 끝점 업데이트로 끝점을 업데이트합니다.](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

**--remove** 옵션을 사용하여 az 네트워크 [트래픽 관리자 엔드포인트 업데이트를](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) 실행하여 IP 주소 범위를 제거할 수 있습니다.

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>다음 단계
Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 방법에](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) 대해 알아보기