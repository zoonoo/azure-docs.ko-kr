---
title: '지점 및 사이트 간 사용자 지정 IPsec 정책 만들기 및 설정: PowerShell'
titleSuffix: Azure VPN Gateway
description: 이 문서는 VPN Gateway P2S 구성에 대 한 사용자 지정 IPSec 정책을 만들고 설정 하는 데 도움이 됩니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: 852ff5e6ad847ff33f8e32b4c3d9f8b325b80716
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664520"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site"></a>지점 및 사이트 간 사용자 지정 IPsec 정책 만들기 및 설정

사용자 환경에 암호화를 위한 사용자 지정 IPsec 정책이 필요한 경우 필요한 설정을 사용 하 여 정책 개체를 쉽게 구성할 수 있습니다. 이 문서에서는 사용자 지정 정책 개체를 만들고 PowerShell을 사용 하 여 설정 하는 방법을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>전제 조건

사용자 환경이 다음과 같은 필수 구성 요소를 충족 하는지 확인 합니다.

* 작동 지점 및 사이트 간 VPN이 이미 구성 되어 있습니다. 그렇지 않은 경우 [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)또는 [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 사용 하 여 **지점 및 사이트 간 VPN 만들기** 문서 단계를 사용 하 여 구성 합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. 변수 설정

사용할 변수를 선언합니다. 필요한 경우 사용자 고유의 값을 대체 하 여 다음 샘플을 사용 합니다. 연습 중에 언제 든 지 PowerShell/Cloud Shell 세션을 닫는 경우 값을 복사 하 여 다시 붙여넣어 변수를 다시 선언 합니다.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. 정책 개체 만들기

사용자 지정 IPsec 정책 개체를 만듭니다. 필요한 조건에 맞게 값을 조정할 수 있습니다.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. 게이트웨이를 업데이트 하 고 정책을 설정 합니다.

이 단계에서는 기존 P2S VPN 게이트웨이를 업데이트 하 고 IPsec 정책을 설정 합니다.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>다음 단계

P2S 구성에 대 한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조 하세요.