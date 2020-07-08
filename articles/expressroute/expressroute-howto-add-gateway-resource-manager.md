---
title: 'Azure Express 경로: VNet에 게이트웨이 추가: PowerShell'
description: 이 문서에서는 ExpressRoute에 대해 이미 만들어진 Resource Manager VNet에 VNet 게이트웨이를 추가합니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 58d2949a18bfbf2800ae6ab4ac74b02b05b3eb07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736393"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>PowerShell을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

이 문서에서는 기존 VNet에 대한 VNet(가상 네트워크) 게이트웨이를 추가, 크기 조정 및 제거합니다. 이 구성에 대 한 단계는 Express 경로 구성의 리소스 관리자 배포 모델을 사용 하 여 만든 Vnet에 적용 됩니다. 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)를 참조하세요.

## <a name="before-beginning"></a>시작하기 전에

### <a name="working-with-powershell"></a>PowerShell 사용

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>구성 참조 목록

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. [Virtual Network를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요.
