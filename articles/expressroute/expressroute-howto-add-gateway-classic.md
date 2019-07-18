---
title: 'ExpressRoute에 대한 VNet 게이트웨이 구성 - 클래식: Azure PowerShell | Microsoft Docs'
description: ExpressRoute 구성을 위해 PowerShell을 사용하여 클래식 배포 모델 VNet에 대한 VNet 게이트웨이 구성
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 101e03d07a15e9058ef236a575251b052017db32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60365152"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell을 사용하여 ExpressRoute용 가상 네트워크 게이트웨이 구성(클래식)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

이 문서에서는 기존 VNet에 대한 가상 네트워크(VNet) 게이트웨이를 추가하고, 크기를 조정하고, 제거하는 단계를 안내합니다. 이 구성 단계는 특히 **클래식 배포 모델**을 사용하여 만들었으며 ExpressRoute 구성에서 사용되는 VNet을 위한 단계입니다. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>시작하기 전에
이 구성에 필요한 Azure PowerShell cmdlet을 설치했는지 확인합니다(1.0.2 이상). Cmdlet을 설치하지 않은 경우 구성 단계를 시작하기 전에 설치해야 합니다. Azure PowerShell 설치에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. [Virtual Network를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-classic.md)을 참조하세요.

