---
title: 공용 IP 주소 업그레이드
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 기본에서 표준으로 업그레이드합니다.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598417"
---
# <a name="upgrade-public-ip-addresses"></a>공용 IP 주소 업그레이드

SKU로 만든 Azure 공용 IP 주소(기본 또는 표준)는 기능의 측면(할당 방법, 기능 지원, 연결할 수 있는 리소스 등)을 결정합니다. 

이 문서에서는 다음 시나리오를 검토합니다.
* 기본 SKU 공용 IP를 표준 SKU 공용 IP로 업그레이드하는 방법(PowerShell 또는 CLI 사용)
* 클래식 Azure의 예약된 IP를 Azure Resource Manager의 기본 SKU 공용 IP로 마이그레이션하는 방법

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>기본 SKU에서 표준 SKU로 공용 IP 주소 업그레이드하기

공용 IP를 업그레이드하려면 리소스와 연결되지 않아야 합니다. 공용 IP 연결을 해제하는 방법에 대한 자세한 내용은 [이 페이지](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.

>[!IMPORTANT]
>기본에서 표준 SKU로 업그레이드된 공용 IP는 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)을 보장하지 않습니다.  IP 주소를 연결할 리소스를 선택할 때는 기억하세요.

---
# <a name="basic-to-standard---powershell"></a>[**기본에서 표준으로 - PowerShell**](#tab/option-upgrade-powershell)

다음 예에서는 [이 페이지](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic)의 예제를 통해 기본 SKU 공용 IP(**myResourceGroup** 에서 기본 공용 IP **myBasicPublicIP** 사용)를 이전에 만든 것으로 가정합니다.

IP를 업그레이드하려면 간단하게 PowerShell을 사용하여 아래 명령을 실행하세요.  고정 IP 주소가 이미 할당된 경우에는 해당 섹션을 건너뛸 수 있습니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**기본에서 표준으로 - CLI**](#tab/option-upgrade-cli)

다음 예에서는 [이 페이지](./create-public-ip-cli.md?tabs=option-create-public-ip-basic)의 예제를 통해 기본 SKU 공용 IP(**myResourceGroup** 에서 기본 공용 IP **myBasicPublicIP** 사용)를 이전에 만든 것으로 가정합니다.

IP를 업그레이드하려면 간단하게 Azure CLI를 사용하여 아래 명령을 실행하세요.  고정 IP 주소가 이미 할당된 경우에는 해당 섹션을 건너뛸 수 있습니다.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>클래식 예약된 IP를 고정 공용 IP로 업그레이드하기(마이그레이션)

Azure Resource Manager의 새로운 기능을 활용해 클래식 모델에서 최신 Azure Resource Manager 모델로 기존 공용 고정 IP 주소(예약된 IP)를 마이그레이션할 수 있습니다.  마이그레이션된 공용 IP는 기본 SKU 형식입니다.


---

# <a name="reserved-to-basic---powershell"></a>[**기본으로 예약 - PowerShell**](#tab/option-migrate-powershell)

다음 예에서는 **myResourceGroup** 의 이전에 만든 클래식 Azure 예약된 IP **myReservedIP** 를 가정합니다. 마이그레이션을 위해서는 Azure Resource Manager 구독이 마이그레이션용으로 등록되었는지 확인할 필요가 있습니다. 자세한 내용은 [이 페이지](../virtual-machines/migration-classic-resource-manager-ps.md)의 3단계와 4단계를 참조하세요.

예약된 IP를 마이그레이션하려면 PowerShell을 사용하여 아래 명령을 실행합니다.  참고 IP 주소가 서비스와 연결되지 않은 경우(아래 **myService** 로 표시된 서비스), 해당 단계를 건너뛸 수 있습니다.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에 성공하면 다음 단계를 진행하여 마이그레이션을 준비하고 커밋할 수 있습니다.
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP(위의 예에서는 리소스 그룹 **myReservedIP-마이그레이션됨**) 이름으로 만들어집니다.

# <a name="reserved-to-basic---cli"></a>[**기본으로 예약 - CLI**](#tab/option-migrate-cli)

다음 예에서는 **myResourceGroup** 의 이전에 만든 클래식 Azure 예약된 IP **myReservedIP** 를 가정합니다. 마이그레이션을 위해서는 Azure Resource Manager 구독이 마이그레이션용으로 등록되었는지 확인할 필요가 있습니다. 자세한 내용은 [이 페이지](../virtual-machines/migration-classic-resource-manager-cli.md)의 3단계와 4단계를 참조하세요.

예약된 IP를 마이그레이션하려면 Azure CLI를 사용하여 아래 명령을 실행합니다.  IP 주소가 모든 서비스(아래에 **myService** 라는 이름의 서비스와 **myDeployment** 라는 이름의 배포가 있음)와 연결되지 않은 경우, 해당 단계를 건너뛸 수 있습니다.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에 성공하면 다음 단계를 진행하여 마이그레이션을 준비하고 커밋할 수 있습니다.
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP(위의 예에서는 리소스 그룹 **myReservedIP-마이그레이션됨**) 이름으로 만들어집니다.

---

## <a name="limitations"></a>제한 사항

* 기본 공용 IP를 업그레이드하기 위해 Azure 리소스에 연결할 수 없습니다.  공용 IP 연결을 해제하는 방법에 대한 자세한 내용은 [이 페이지](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.  마찬가지로 예약된 IP를 마이그레이션하기 위해 클라우드 서비스에 연결할 수 없습니다.  예약된 IP 연결을 해제하는 방법에 대한 자세한 내용은 [이 페이지](./remove-public-ip-address-vm.md)를 참조하세요.  
* 기본에서 표준 SKU로 업그레이드된 공용 IP는 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)을 보장하지 않습니다.  IP 주소를 연결할 리소스를 선택할 때는 기억하세요.
* 표준에서 기본으로의 다운그레이드는 불가능합니다.

## <a name="next-steps"></a>다음 단계

- SKU 유형 간의 차이와 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)을 포함한 Azure의 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대한 자세한 정보.
- [Azure 퍼블릭 부하 분산 장치를 기본에서 표준으로 업그레이드](../load-balancer/upgrade-basic-standard.md)하는 방법 알아보기.
- [클래식 Azure 예약된 IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) 및 [클래식 리소스를 Azure Resource Manager로 마이그레이션](../virtual-machines/migration-classic-resource-manager-overview.md) 이해하기.
