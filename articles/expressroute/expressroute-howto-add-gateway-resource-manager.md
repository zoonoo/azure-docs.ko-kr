---
title: 'ExpressRoute에 대한 VNet에 가상 네트워크 게이트웨이 추가: PowerShell: Azure | Microsoft Docs'
description: 이 문서에서는 ExpressRoute에 대해 이미 만들어진 Resource Manager VNet에 VNet 게이트웨이를 추가합니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097916"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>PowerShell을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

이 문서에서는 기존 VNet에 대한 VNet(가상 네트워크) 게이트웨이를 추가, 크기 조정 및 제거합니다. 이 구성에서 수행하는 단계는 Resource Manager 배포 모델을 사용하여 만든 VNet(ExpressRoute 구성에서 사용됨)에만 해당합니다. 가상 네트워크 게이트웨이 및 ExpressRoute의 게이트웨이 구성 설정에 대한 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)를 참조하세요. 


## <a name="before-beginning"></a>시작하기 전에
최신 Azure PowerShell cmdlet를 설치했는지 확인합니다. cmdlet을 설치하지 않은 경우 구성 단계를 시작하기 전에 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/overview)을 참조하세요.

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. [Virtual Network를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요.

