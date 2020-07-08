---
title: 'Azure Express 경로: VNet에 게이트웨이 추가: 클래식'
description: ExpressRoute 구성을 위해 PowerShell을 사용하여 클래식 배포 모델 VNet에 대한 VNet 게이트웨이 구성
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bcffd60596f8c2286b91eaf13be3e09bb8bec94f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736410"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell을 사용하여 ExpressRoute용 가상 네트워크 게이트웨이 구성(클래식)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

이 문서에서는 기존 VNet에 대한 가상 네트워크(VNet) 게이트웨이를 추가하고, 크기를 조정하고, 제거하는 단계를 안내합니다. 이 구성 단계는 특히 **클래식 배포 모델**을 사용하여 만들었으며 ExpressRoute 구성에서 사용되는 VNet을 위한 단계입니다. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>시작하기 전에
이 구성에 필요한 Azure PowerShell cmdlet을 설치 했는지 확인 합니다.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. [Virtual Network를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-classic.md)을 참조하세요.

