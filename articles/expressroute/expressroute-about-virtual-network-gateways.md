---
title: "ExpressRoute 가상 네트워크 게이트웨이 정보 | Microsoft 문서"
description: "ExpressRoute의 가상 네트워크 게이트웨이에 대해 알아봅니다."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a575929f2c42a00b6e6cacd86318253d20b6b51e


---
# <a name="about-virtual-network-gateways-for-expressroute"></a>ExpressRoute에 대한 가상 네트워크 게이트웨이 정보
가상 네트워크 게이트웨이는 Azure 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용됩니다. ExpressRoute 연결을 구성할 때 가상 네트워크 게이트웨이 및 가상 네트워크 게이트웨이 연결을 만들고 구성해야 합니다.

가상 네트워크 게이트웨이를 만들 때 몇 가지 설정을 지정합니다. 필수 설정 중 하나에서는 ExpressRoute 또는 사이트 간 VPN 트래픽에 게이트웨이를 사용할지 여부를 지정합니다. Resource Manager 배포 모델에서 이 설정은 '-GatewayType'입니다.

네트워크 트래픽을 전용 개인 연결에 전송하면 'Express 경로' 게이트웨이 유형을 사용합니다. 이를 Express 경로 게이트웨이라고도 합니다. 네트워크 트래픽을 공용 인터넷을 통해 암호화하여 전송하면 'Vpn' 게이트웨이 유형을 사용합니다. 이를 VPN Gateway라고도 합니다. 사이트 간, 지점 및 사이트 간, VNet 간 연결은 모두 VPN Gateway를 사용합니다. 

각각의 가상 네트워크에는 게이트웨이 유형당 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 예를 들어 -GatewayType Vpn을 사용하는 하나의 가상 네트워크 게이트웨이와 -GatewayType Express 경로를 사용하는 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 이 문서에서는 ExpressRoute 가상 네트워크 게이트웨이를 중점적으로 다룹니다.

## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>게이트웨이 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

게이트웨이를 보다 강력한 게이트웨이 SKU로 업그레이드하려면 대부분의 경우 'Resize-AzureRmVirtualNetworkGateway' PowerShell cmdlet을 사용할 수 있습니다. 표준 및 고성능 SKU로 업그레이드하는 경우에 가능합니다. 하지만 초고성능 SKU로 업그레이드하려면 게이트웨이를 다시 만들어야 합니다.

### <a name="a-nameaggthroughputaestimated-aggregate-throughput-by-gateway-sku"></a><a name="aggthroughput"></a>게이트웨이 SKU에 의해 예상된 총 처리량
다음 테이블에서는 게이트웨이 형식과 예상 총 처리량을 보여 줍니다. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API 및 PowerShell cmdlet
가상 네트워크 게이트웨이 구성을 위해 REST API와 PowerShell cmdlet을 사용할 경우 추가 기술 리소스 및 특정 구문 요구 사항에 대해서는 다음 페이지를 참조하세요.

| **클래식** | **리소스 관리자** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>다음 단계
사용 가능한 연결 구성에 대한 자세한 내용은 [ExpressRoute 개요](expressroute-introduction.md) 를 참조하세요. 




<!--HONumber=Nov16_HO3-->


