---
title: Azure Virtual WAN을 사용하여 Azure 및 온-프레미스 환경에 대한 ExpressRoute 연결 생성 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure 및 온-프레미스 환경에 대한 ExpressRoute 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 7b7adcc85b9274af45ddab653e875377e959e40c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876329"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>자습서: Azure Virtual WAN을 사용하여 ExpressRoute 연결 만들기(미리 보기)

이 자습서에서는 가상 WAN을 사용하여 ExpressRoute 회로 및 연결을 통해 Azure에서 리소스에 연결하는 방법을 보여줍니다. 가상 WAN에 대한 자세한 내용은 [가상 WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * vWAN 만들기
> * 허브 만들기
> * 회로를 찾아 허브에 연결
> * 회로를 허브에 연결
> * 허브에 VNet 연결
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>이 기능 등록

Virtual WAN을 구성하려면 그 전에 먼저 미리 보기에서 구독을 등록해야 합니다. 그렇지 않으면 포털에서 Virtual WAN을 사용할 수 없습니다. 등록하려면 구독 ID를 포함하는 이메일을 **azurevirtualwan\@microsoft.com**으로 보냅니다. 구독이 등록되면 이메일 회신을 받게 됩니다.

**미리 보기 고려 사항:**

ExpressRoute 회로는 [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported)를 지원하는 국가에서 사용하도록 설정해야 합니다.

## <a name="vnet"></a>1. 가상 네트워크 만들기

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. 가상 WAN 만들기

브라우저에서 [Azure Portal(미리 보기)](https://aka.ms/azurevirtualwanpreviewfeatures)로 이동하고 Azure 계정으로 로그인합니다.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>시작 페이지

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. 허브 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. 회로를 찾아 허브에 연결

1. **Virtual WAN Architecture**에서 vWAN을 선택하고 **ExpressRoute 회로**를 선택합니다.
1. ExpressRoute 회로가 vWAN과 동일한 구독에 있으면 구독에서 **ExpressRoute 회로 선택**을 클릭합니다. 
1. 풀 다운을 사용하여 허브에 연결할 ExpressRoute를 선택합니다.
1. ExpressRoute 회로가 동일한 구독에 없거나 [인증 키와 피어 ID](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)가 제공된 경우 **권한 키를 복구하는 회로 찾기**를 선택합니다.
1. 다음 세부 정보를 입력합니다.
1. **인증 키** - 위에 설명한 대로 회로 소유자가 생성
1. **피어 회로 URI** -회로 소유자가 제공하고 회로의 고유 ID인 회로 URI
1. **라우팅 가중치** - [라우팅 가중치](../expressroute/expressroute-optimize-routing.md)를 사용하면 여러 다른 피어링 lcoations의 여러 회로가 동일한 허브에 연결될 때 특정 경로를 기본으로 설정할 수 있습니다.
1. **회로 찾기**를 클릭하고 찾으면 회로를 선택합니다.
1. 드롭다운에서 1개 이상의 허브를 선택하고 **저장**을 클릭합니다.

## <a name="vnet"></a>5. 허브에 VNet 연결

이 단계에서는 허브와 VNet 간에 피어링 연결을 만듭니다. 연결하려는 각 VNet에 대해 이 단계를 반복합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.


## <a name="viewwan"></a>6. 가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>7. 리소스 상태 보기

1. WAN으로 이동합니다.
2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.

## <a name="connectmon"></a>8. 연결 모니터링

Azure VM과 원격 사이트 간의 통신을 모니터링하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP이고 대상 IP는 사이트 IP입니다.

## <a name="cleanup"></a>9. 리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * vWAN 만들기
> * 허브 만들기
> * 회로를 찾아 허브에 연결
> * 회로를 허브에 연결
> * 허브에 VNet 연결
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
