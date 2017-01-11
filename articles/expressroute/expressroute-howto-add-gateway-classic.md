---
title: "PowerShell을 사용하여 ExpressRoute용 VNet 게이트웨이 구성 | Microsoft 문서"
description: "Express 경로 구성을 위해 PowerShell을 사용하여 클래식 배포 모델 VNet에 대한 VNet 게이트웨이 구성"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 4acb64838288d36f0dc1b1eb9736b00faef21a0c
ms.openlocfilehash: 4b05e12c2b30f1e8aa3efc7cd47cb79c4958dc1e


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>클래식 배포 모델 및 PowerShell을 사용하여 Express 경로에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
> * [PowerShell - 클래식](expressroute-howto-add-gateway-classic.md)
> 
> 

이 문서에서는 기존 VNet에 대한 가상 네트워크(VNet) 게이트웨이를 추가하고, 크기를 조정하고, 제거하는 단계를 안내합니다. 이 구성 단계는 특히 **클래식 배포 모델** 을 사용하여 만들었으며 Express 경로 구성에서 사용할 VNet을 위한 단계입니다. 

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>시작하기 전에
이 구성에 필요한 Azure PowerShell cmdlet을 설치했는지 확인합니다(1.0.2 이상). Cmdlet을 설치하지 않은 경우 구성 단계를 시작하기 전에 설치해야 합니다. Azure PowerShell 설치에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 Express 경로 회로에 연결할 수 있습니다. [가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-classic.md)을 참조하세요.




<!--HONumber=Dec16_HO1-->


