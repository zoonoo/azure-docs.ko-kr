---
title: 'Azure VPN Gateway에 대해 OpenVPN을 구성하는 방법: PowerShell| Microsoft Docs'
description: Azure VPN Gateway에 대해 OpenVPN을 구성하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 1dad960b0877cddf3be9afc01e3e687ebe4702c0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357830"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Azure 지점-사이트 간 VPN Gateway(미리 보기)에 대해 OpenVPN 구성

이 문서는 Azure VPN Gateway에서 OpenVPN을 설정하는 데 도움이 됩니다. 이 문서에서는 작동하는 지점-사이트 간 환경이 이미 있다고 가정합니다. 환경이 없는 경우 1단계의 지침을 사용하여 지점-사이트 간 VPN을 만듭니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register"></a>이 기능 등록

다음 단계에서 **TryIt**을 클릭하고 Azure Cloud Shell을 사용하여 간편하게 이 기능을 등록하세요.

>[!NOTE]
>이 기능을 등록하지 않으면 사용할 수 없습니다.
>

**TryIt**을 클릭하여 Azure Cloud Shell을 연 후, 다음 명령을 복사하여 붙여넣습니다.

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

기능이 등록되었다고 표시되면 Microsoft.Network 네임스페이스에 대한 구독을 등록합니다.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. 지점-사이트 간 VPN 만들기

작동하는 지점-사이트 간 환경이 아직 없는 경우 지침에 따라 환경을 만듭니다. 기본 Azure 인증서 인증을 사용하여 지점-사이트 간 VPN 게이트웨이를 만들고 구성하려면 [지점-사이트 간 VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하세요. 기본 SKU는 IKEv2 지점 및 사이트 간에 대해 지원되지 않습니다.

## <a name="cmdlets"></a>2. PowerShell cmdlet 설치

최신 버전의 Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요. 이는 이 연습에 필요한 현재 값이 이전 버전의 cmdlet에 포함되어 있지 않으므로 중요합니다.

## <a name="enable"></a>3. 게이트웨이에서 OpenVPN 사용

게이트웨이에서 OpenVPN을 사용하도록 설정합니다. 다음 명령을 실행하기 전에 게이트웨이가 지점-사이트 간(IKEv2 또는 SSTP)에 대해 이미 구성되어 있는지 확인합니다.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>다음 단계

OpenVPN 클라이언트를 구성하려면 [OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md)을 참조하세요.
