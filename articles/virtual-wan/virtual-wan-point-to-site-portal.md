---
title: Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 연결 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 84f8563a6b03f10f4cbc647426c350d9fac52780
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812687"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용하여 사용자 VPN 연결 만들기

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv2) 또는 OpenVPN VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형은 클라이언트 컴퓨터에서 클라이언트를 구성해야 합니다. 가상 WAN에 대한 자세한 내용은 [가상 WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * WAN 만들기
> * P2S 구성 만들기
> * 허브 만들기
> * DNS 서버 지정
> * VPN 클라이언트 프로필 다운로드
> * 가상 WAN 보기

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>P2S 구성 만들기

지점 및 사이트 간(P2S) 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>지점 및 사이트 간 게이트웨이를 사용하여 허브 만들기

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS 서버 지정

Virtual WAN 사용자 VPN 게이트웨이를 사용하여 최대 5대의 DNS 서버를 지정할 수 있습니다. 허브를 만드는 동안 이 작업을 구성하거나 나중에 수정할 수 있습니다. 이렇게 하려면 먼저 가상 허브를 찾습니다. **사용자 VPN(지점 및 사이트 간)** 에서 **구성**을 선택하고 DNS 서버 IP 주소를 **사용자 지정 DNS 서버** 텍스트 상자에 입력합니다.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="사용자 지정 DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>사용자 VPN 클라이언트 구성

다운로드한 프로필을 사용하여 원격 액세스 클라이언트를 구성합니다. 각 운영 체제에 대한 프로시저가 다르므로 시스템에 적용되는 지침을 따르세요.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
1. **개요** 페이지의 맵에 있는 각 점은 허브를 나타냅니다.
1. **허브 및 연결** 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
