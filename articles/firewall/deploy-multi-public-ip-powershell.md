---
title: Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽 배포
description: 이 문서에서는 Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703977"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 여러 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포

> [!IMPORTANT]
> 여러 공용 IP 주소를 사용 하 여 azure 방화벽은 Azure PowerShell, Azure CLI, REST 및 템플릿을 통해 사용할 수 있습니다. 포털 사용자 인터페이스 증분 영역에 추가 되 고 롤아웃이 완료 되 면 모든 지역에서 제공 됩니다.

최대 100 개의 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포할 수 있습니다.

이 기능을 사용 하면 다음과 같은 경우:

- **DNAT** -백 엔드 서버에 여러 표준 포트 인스턴스를 변환할 수 있습니다. 예를 들어 두 개의 공용 IP 주소에 있는 경우 두 IP 주소에 대 한 TCP 포트 3389(rdp)를 번역할 수 있습니다.
- **SNAT** -추가 포트를 SNAT 포트 소모가 발생할 가능성이 줄어듭니다 아웃 바운드 SNAT 연결에 사용할 수 있습니다. 이때 Azure 방화벽 연결에 사용할 원본 공용 IP 주소를 임의로 선택 합니다. 네트워크에서 다운스트림 필터링에 있는 경우 방화벽을 사용 하 여 연결 된 모든 공용 IP 주소를 허용 해야 합니다.

다음 Azure PowerShell 예제를 구성에 추가 하는 방법 Azure 방화벽에 대 한 공용 IP 주소 제거를 보여 줍니다.

> [!NOTE]
> Azure 방화벽 공용 IP 주소 구성 페이지에서 첫 번째 ipConfiguration을 제거할 수 없습니다. IP 주소를 수정 하려는 경우에 Azure PowerShell을 사용할 수 있습니다.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>두 개 이상의 공용 IP 주소를 사용 하 여 방화벽 만들기

이 예제에서는 가상 네트워크에 연결 된 방화벽을 만듭니다 *vnet* 두 개의 공용 IP 주소를 사용 하 여 합니다.

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

이 예제에서는 공용 IP 주소 *azFwPublicIp1* 방화벽에 연결 됩니다.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>기존 방화벽에서 공용 IP 주소를 제거 합니다.

이 예제에서는 공용 IP 주소 *azFwPublicIp1* 방화벽에서 분리 됩니다.

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

* [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
