---
title: 공용 IP 주소 업그레이드
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 Basic에서 Standard로 업그레이드 합니다.
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
ms.date: 10/07/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 0c248149694c2bf66b8c94e9c0a29a8f7da9f4e4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843743"
---
# <a name="upgrade-public-ip-addresses"></a>공용 IP 주소 업그레이드

Azure 공용 IP 주소는 SKU (기본 또는 표준)를 사용 하 여 생성 됩니다 .이를 통해 기능 (할당 방법, 가용성 영역 간 사용 및 연결할 수 있는 리소스 등)을 결정 합니다. 

이 문서에서는 다음 시나리오를 검토 합니다.
* 기본 SKU 공용 IP를 표준 SKU 공용 IP로 업그레이드 하는 방법 (포털, PowerShell 또는 CLI 사용)
* 클래식 Azure 예약된 IP Azure Resource Manager 기본 SKU 공용 IP로 마이그레이션하는 방법

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>기본에서 표준 SKU로 공용 IP 주소 업그레이드

>[!NOTE]
>공용 Ip를 Basic에서 Standard로 업그레이드 하는 기능은 일부 지역에서 사용할 수 없습니다.  자세한 내용은 [**제한 사항을**](#limitations) 참조 하세요.

공용 IP를 업그레이드 하려면 리소스와 연결 되지 않아야 합니다. 공용 IP의 연결을 해제 하는 방법에 대 한 자세한 내용은 [이 페이지](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) 를 참조 하세요.

>[!IMPORTANT]
>기본에서 표준 SKU로 업그레이드 된 공용 Ip에는 계속 사용할 수 있는 [가용성 영역이](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)없습니다.  즉, 영역 중복 이거나이가 제공 된 지역에서 미리 지정 된 영역에 연결 된 Azure 리소스에 연결할 수 없습니다.

---
# <a name="basic-to-standard---powershell"></a>[**기본에서 표준으로-PowerShell**](#tab/option-upgrade-powershell)

다음 예제에서는 **Myresourcegroup** 에서 기본 공용 Ip **mybasicpublicip** 를 사용 하 여 [이 페이지](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) 에 제공 된 예제를 사용 하 여 기본 SKU 공용 ip를 이전에 만든 것으로 가정 합니다.

IP를 업그레이드 하려면 PowerShell을 사용 하 여 아래 명령을 실행 하기만 하면 됩니다.  참고 IP 주소가 이미 정적으로 할당 된 경우에는 해당 섹션을 건너뛸 수 있습니다.

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

# <a name="basic-to-standard---cli"></a>[**기본에서 표준 CLI로**](#tab/option-upgrade-cli)

다음 예제에서는 **Myresourcegroup** 에서 기본 공용 Ip **mybasicpublicip** 를 사용 하 여 [이 페이지](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) 에 제공 된 예제를 사용 하 여 기본 SKU 공용 ip를 이전에 만든 것으로 가정 합니다.

IP를 업그레이드 하려면 Azure CLI를 사용 하 여 아래 명령을 실행 하면 됩니다.  참고 IP 주소가 이미 정적으로 할당 된 경우에는 해당 섹션을 건너뛸 수 있습니다.

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

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>클래식 예약된 IP를 고정 공용 IP로 업그레이드 (마이그레이션)

Azure Resource Manager의 새로운 기능을 활용 하기 위해 클래식 모델에서 최신 Azure Resource Manager 모델로 기존 공용 고정 IP 주소 (예약 된 Ip)를 마이그레이션할 수 있습니다.  마이그레이션된 공용 IP는 기본 SKU 유형입니다.


---

# <a name="reserved-to-basic---powershell"></a>[**기본으로 예약 됨-PowerShell**](#tab/option-migrate-powershell)

다음 예에서는 **Myreservedip** 에서 클래식 Azure 예약된 IP **myreservedip** 를 이전에 만든 것으로 가정 합니다. 마이그레이션에 대 한 다른 필수 구성 요소는 Azure Resource Manager 구독이 마이그레이션을 위해 등록 되었는지 확인 하는 것입니다. 이에 대해서는 [이 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps)의 3 단계와 4 단계에 대해 자세히 설명 합니다.

예약된 IP 마이그레이션하기 위해 PowerShell을 사용 하 여 아래 명령을 실행 합니다.  참고 IP 주소가 서비스와 연결 되지 않은 경우 (아래에 **myService** 라는 서비스가 있음) 해당 단계를 건너뛸 수 있습니다.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
이전 명령은 마이그레이션을 차단 하는 모든 경고 및 오류를 표시 합니다. 유효성 검사에 성공 하면 다음 단계를 진행 하 여 마이그레이션을 준비 하 고 커밋할 수 있습니다.
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP의 이름을 사용 하 여 만들어집니다. 위의 예에서는 리소스 그룹 **Myreservedip-마이그레이션됩니다**.

# <a name="reserved-to-basic---cli"></a>[**기본-CLI로 예약 됨**](#tab/option-migrate-cli)

다음 예에서는 **Myreservedip** 에서 클래식 Azure 예약된 IP **myreservedip** 를 이전에 만든 것으로 가정 합니다. 마이그레이션에 대 한 다른 필수 구성 요소는 Azure Resource Manager 구독이 마이그레이션을 위해 등록 되었는지 확인 하는 것입니다. 이에 대해서는 [이 페이지](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli)의 3 단계와 4 단계에 대해 자세히 설명 합니다.

예약된 IP 마이그레이션하려면 Azure CLI를 사용 하 여 아래 명령을 실행 합니다.  IP 주소가 서비스와 연결 되지 않은 경우 (아래에 **myService** 및 배포 **mydeployment** 라는 서비스가 있음) 해당 단계를 건너뛸 수 있습니다.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
이전 명령은 마이그레이션을 차단 하는 모든 경고 및 오류를 표시 합니다. 유효성 검사에 성공 하면 다음 단계를 진행 하 여 마이그레이션을 준비 하 고 커밋할 수 있습니다.
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP의 이름을 사용 하 여 만들어집니다. 위의 예에서는 리소스 그룹 **Myreservedip-마이그레이션됩니다**.

---

## <a name="limitations"></a>제한 사항

* 이 기능은 현재 다음 지역에서 사용할 수 없습니다.<br>
US Gov 버지니아<br>
US DoD 동부<br>
US DoD 중부<br>
중국 동부<br>
중국 동부 2<br>
중국 북부<br>
중국 북부 2

* 기본 공용 IP를 업그레이드 하기 위해 Azure 리소스에 연결할 수 없습니다.  공용 Ip의 연관을 해제 하는 방법에 대 한 자세한 내용은 [이 페이지](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) 를 참조 하세요.  마찬가지로 예약된 IP 마이그레이션하기 위해 클라우드 서비스에 연결할 수 없습니다.  예약 된 Ip의 연관을 해제 하는 방법에 대 한 자세한 내용은 [이 페이지](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) 를 참조 하세요.  
* 기본에서 표준 SKU로 업그레이드 된 공용 Ip는 [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) 을 계속 갖지 않으므로 영역 중복 또는 영역 인 Azure 리소스에 연결할 수 없습니다.  참고이는 가용성 영역을 제공 하는 지역에만 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure의 [공용](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) ip 주소에 대해 자세히 알아보세요. 여기에는 SKU 유형 간의 차이와 [공용 ip 주소 설정이](virtual-network-public-ip-address.md#create-a-public-ip-address)포함 됩니다.
- [Azure 공용 부하 분산 장치를 Basic에서 Standard로 업그레이드](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)하는 방법을 알아봅니다.
- [클래식 Azure 예약 된 ip](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) 를 이해 하 고 [클래식 리소스를 Azure Resource Manager로 마이그레이션해야](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)합니다.
