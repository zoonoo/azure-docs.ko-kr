---
title: PowerShell을 사용하여 여러 공용 IP 주소로 Azure 방화벽 배포
description: 이 문서에서는 Azure PowerShell을 사용하여 여러 공용 IP 주소가 있는 Azure 방화벽을 배포하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314024"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell을 사용하여 여러 공용 IP 주소로 Azure Firewall 배포

이 기능을 사용하면 다음과 같은 시나리오를 사용할 수 있습니다.

- **DNAT** - 여러 표준 포트 인스턴스를 백 엔드 서버로 변환할 수 있습니다. 예를 들어 공용 IP 주소가 2개인 경우 두 IP 주소 모두에 대해 TCP 포트 3389(RDP)를 변환할 수 있습니다.
- **SNAT** - 아웃바운드 SNAT 연결에 추가 포트를 사용할 수 있기 때문에 SNAT 포트가 고갈될 가능성이 줄어듭니다. 이때 Azure Firewall은 연결에 사용할 원본 공용 IP 주소를 임의로 선택합니다. 네트워크에 다운스트림 필터링이 있는 경우, 방화벽과 연결된 모든 공용 IP 주소를 허용해야 합니다.
 
여러 공용 IP 주소를 사용하는 Azure Firewall은 Azure Portal, Azure PowerShell, Azure CLI, REST 및 템플릿을 통해 사용할 수 있습니다. 최대 100개의 공용 IP 주소가 있는 Azure 방화벽을 배포할 수 있습니다.

다음 Azure PowerShell 예제에서는 Azure 방화벽에 대한 공용 IP 주소를 구성, 추가 및 제거하는 방법을 보여 주십니다.

> [!NOTE]
> Azure 방화벽 공용 IP 주소 구성 페이지에서 첫 번째 ipConfiguration를 제거할 수 없습니다. IP 주소를 수정하려면 Azure PowerShell을 사용할 수 있습니다.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>두 개 이상의 공용 IP 주소가 있는 방화벽 만들기

이 예제는 두 개의 공용 IP 주소가 있는 가상 네트워크 *vnet에* 연결된 방화벽을 만듭니다.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>기존 방화벽에 공용 IP 주소 추가

이 예제에서는 공용 IP 주소 *azFwPublicIp1이* 방화벽에 첨부됩니다.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>기존 방화벽에서 공용 IP 주소 제거

이 예제에서는 공용 IP 주소 *azFwPublicIp1이* 방화벽에서 분리됩니다.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 여러 공용 IP 주소가 있는 Azure 방화벽 만들기 - 리소스 관리자 템플릿](quick-create-multiple-ip-template.md)
