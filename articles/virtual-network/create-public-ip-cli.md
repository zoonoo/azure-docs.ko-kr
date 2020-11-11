---
title: 공용 IP 만들기-Azure CLI
description: Azure CLI를 사용 하 여 공용 IP를 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 3e6e060f4af2addba66ed02e82d76dd520375f25
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516410"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>빠른 시작: Azure CLI을 사용 하 여 공용 IP 주소 만들기

이 문서에서는 Azure CLI를 사용 하 여 공용 IP 주소 리소스를 만드는 방법을 보여 줍니다. 이에 연결할 수 있는 리소스, 기본 및 표준 SKU 간의 차이점 및 기타 관련 정보에 대 한 자세한 내용은 [공용 IP 주소](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)를 참조 하세요.  이 예에서는 IPv4 주소에만 중점을 둡니다. IPv6 주소에 대 한 자세한 내용은 [Azure VNet 용 ipv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)을 참조 하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**Eastus2** 위치에 **myresourcegroup** 이라는 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 를 사용 하 여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**표준 SKU-영역 사용**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>다음 명령은 API 버전 2020-08-01 이상에 대해 작동 합니다.  현재 사용 중인 API 버전에 대 한 자세한 내용은 [리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)을 참조 하세요.

[Az network 공공 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 를 사용 하 여 **Myresourcegroup** 에 **mystandardzrpublicip** 이라는 표준 영역 중복 공용 ip 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> 2020-08-01 보다 오래 된 API 버전의 경우 영역 매개 변수를 지정 하지 않고 위의 명령을 실행 하 여 영역 중복 IP 주소를 만듭니다. 
>

**Myresourcegroup** 에서 이름이 **myStandardZonalPublicIP** 인 영역 2에 표준 영역 공용 IP 주소를 만들려면 다음 명령을 사용 합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

영역에 대 한 위의 옵션은 [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)있는 지역 에서만 유효한 선택입니다.

# <a name="standard-sku---no-zones"></a>[**표준 SKU-영역 없음**](#tab/option-create-public-ip-standard)

>[!NOTE]
>다음 명령은 API 버전 2020-08-01 이상에 대해 작동 합니다.  현재 사용 중인 API 버전에 대 한 자세한 내용은 [리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)을 참조 하세요.

[Az network 공공 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 를 사용 하 여 **Myresourcegroup** 에서 **mystandardpublicip** 라는 비 영역 리소스로 표준 공용 ip 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
이 선택 항목은 모든 지역에서 유효 하며, [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)없는 지역에서 표준 공용 IP 주소에 대 한 기본 선택입니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-create-public-ip-basic)

[Az network 공공 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 를 사용 하 여 **Myresourcegroup** 에 **mybasicpublicip** 이라는 기본 고정 공용 ip 주소를 만듭니다.  기본 공용 Ip에는 가용성 영역의 개념이 없습니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
시간에 따라 IP 주소를 변경 하는 것이 허용 되는 경우에는 할당 메서드를 ' 동적 '으로 변경 하 여 **동적** ip 할당을 선택할 수 있습니다.

---

## <a name="additional-information"></a>추가 정보 

위에 나열 된 개별 변수에 대 한 자세한 내용은 [공용 IP 주소 관리](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [가상 컴퓨터에 공용 IP 주소](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)를 연결 합니다.
- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
