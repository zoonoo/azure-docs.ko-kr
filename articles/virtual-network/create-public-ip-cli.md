---
title: 공용 IP 만들기 - Azure CLI
description: Azure CLI를 사용하여 공용 IP를 만드는 방법 알아보기
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
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767694"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 공용 IP 주소 만들기

이 문서에서는 Azure CLI를 사용하여 공용 IP 주소 리소스를 만드는 방법을 소개합니다. 연결할 수 있는 리소스, 기본 SKU와 표준 SKU 간의 차이점, 기타 관련 정보에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요.  이 예제에서는 IPv4 주소만 중점적으로 다룹니다. IPv6 주소에 대한 자세한 내용은 [Azure VNet용 IPv6](./ipv6-overview.md)를 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>공용 IP 만들기

---
# <a name="standard-sku---using-zones"></a>[**표준 SKU - 영역 사용**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>다음 명령은 API 버전 2020-08-01이상에 대해 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 **myResourceGroup** 에 **myStandardZRPublicIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 이전 버전의 경우 영역 매개 변수를 지정하지 않고 위의 명령을 실행하여 영역 중복 IP 주소를 만듭니다. 
>

**myResourceGroup** 에서 **myStandardZonalPublicIP** 라는 영역 2에 표준 영역 공용 IP 주소를 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

위의 영역 옵션은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="standard-sku---no-zones"></a>[**표준 SKU - 영역 없음**](#tab/option-create-public-ip-standard)

>[!NOTE]
>다음 명령은 API 버전 2020-08-01이상에 대해 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **myResourceGroup** 에서 **myStandardPublicIP** 라는 비영역 리소스로 표준 공용 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
이 방법은 모든 지역에서 유효하며, [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 방법입니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-create-public-ip-basic)

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **myResourceGroup** 에 **myBasicPublicIP** 라는 기본 고정 공용 IP 주소를 만듭니다.  기본 공용 IP에는 가용성 영역의 개념이 없습니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
시간에 따라 IP 주소를 변경할 수 있는 경우 할당 방법을 ‘동적’으로 변경하여 **동적** IP 할당을 선택할 수 있습니다.

---

## <a name="additional-information"></a>추가 정보 

위에 나열된 개별 변수에 대한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신](./associate-public-ip-address-vm.md#azure-portal)에 연결합니다.
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
