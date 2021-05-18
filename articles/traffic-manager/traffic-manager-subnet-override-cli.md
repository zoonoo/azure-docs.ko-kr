---
title: Azure CLI를 사용하여 Azure Traffic Manager 서브넷 재정의 | Microsoft Docs
description: 이 문서는 Traffic Manager 서브넷 재정의로 Traffic Manager 프로필의 라우팅 방법을 재정의하여 엔드포인트 매핑에 미리 정의된 IP 범위를 통해 최종 사용자의 IP 주소를 기반으로 하는 엔드포인트로 트래픽을 보내는 방법을 이해하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767802"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Azure CLI를 사용하여 Traffic Manager 서브넷 재정의

Traffic Manager 서브넷 재정의를 사용하여 프로필의 라우팅 메서드를 변경할 수 있습니다.  재정의를 추가하면 엔드포인트 매핑에 미리 정의된 IP 범위를 사용하여 최종 사용자의 IP 주소를 기반으로 트래픽을 보냅니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방법

트래픽 관리자 프로필에 서브넷 재정의를 추가하면 Traffic Manager는 먼저 최종 사용자의 IP 주소에 서브넷 재정의가 있는지 확인합니다. 재정의가 발견되는 경우, 사용자의 DNS 쿼리는 해당 엔드포인트로 보내집니다.  매핑이 없으면 Traffic Manager는 프로필의 원래 라우팅 메서드로 대체합니다. 

IP 주소 범위는 CIDR 범위(예: 1.2.3.0/24) 또는 주소 범위(예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 엔드포인트와 연결된 IP 범위는 해당 엔드포인트에 고유해야 합니다. 서로 다른 엔드포인트 간에 IP 범위가 겹치면 Traffic Manager가 프로필을 거부하게 됩니다.

서브넷 재정의를 지원하는 라우팅 프로필에는 다음 두 가지 유형이 있습니다.

* **지리** - Traffic Manager가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 찾으면, 엔드포인트 상태에 상관없이 엔드포인트로 쿼리를 라우팅합니다.
* **성능** - Traffic Manager가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 찾으면, 엔드포인트가 정상 상태인 경우에만 엔드포인트로 트래픽을 라우팅합니다.  서브넷 재정의 엔드포인트가 정상이 아닌 경우, Traffic Manager는 성능 라우팅 추론으로 대체합니다.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager 서브넷 재정의 만들기

Traffic Manager 서브넷 재정의를 만들려면 Azure CLI를 사용하여 재정의할 서브넷을 Traffic Manager 엔드포인트에 추가할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>서브넷 재정의로 Traffic Manager 엔드포인트를 업데이트합니다.
Azure CLI를 사용하여 [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)로 엔드포인트를 업데이트합니다.

```azurecli-interactive
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

**--remove** 옵션으로 [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)를 실행하여 IP 주소 범위를 제거할 수 있습니다.

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>다음 단계

Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 메서드](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)에 대해 알아봅니다.