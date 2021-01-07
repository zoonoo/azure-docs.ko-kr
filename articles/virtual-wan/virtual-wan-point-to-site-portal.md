---
title: '자습서: Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 연결 만들기'
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7e65d5d2941765df98b3bf3b7fb8ff2e89b7e9f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411204"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용하여 사용자 VPN 연결 만들기

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv2) 또는 OpenVPN VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형을 사용하려면 클라이언트 컴퓨터에서 VPN 클라이언트를 구성해야 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 만들기
> * P2S 구성 만들기
> * 가상 허브 만들기
> * DNS 서버 지정
> * VPN 클라이언트 프로필 구성 패키지 생성
> * VPN 클라이언트 구성
> * 가상 WAN 보기

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>P2S 구성 만들기

지점 및 사이트 간(P2S) 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>가상 허브 및 게이트웨이 만들기

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS 서버 지정

허브를 만들 때 이 설정을 구성하거나 나중에 수정할 수 있습니다. 수정하려면 가상 허브를 찾습니다. **사용자 VPN(지점 및 사이트 간)** 에서 **구성** 을 선택하고 DNS 서버 IP 주소를 **사용자 지정 DNS 서버** 텍스트 상자에 입력합니다. 최대 5개의 DNS 서버를 지정할 수 있습니다.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="사용자 지정 DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>VPN 클라이언트 프로필 패키지 생성

VPN 클라이언트를 구성하려면 VPN 클라이언트 프로필 패키지를 생성하고 다운로드합니다.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN 클라이언트 구성

다운로드한 프로필 패키지를 사용하여 원격 액세스 VPN 클라이언트를 구성합니다. 운영 체제마다 절차가 다릅니다. 시스템에 적용되는 지침을 따르세요.
클라이언트 구성이 완료되면 연결할 수 있습니다.

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

다음으로, Virtual WAN에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> * [가상 WAN FAQ](virtual-wan-faq.md)
