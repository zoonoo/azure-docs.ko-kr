---
title: "게이트웨이 연결 확인 | Microsoft Docs"
description: "이 문서에서는 Resource Manager 배포 모델에서 게이트웨이 연결을 확인하는 방법을 보여 줍니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: d1b6d12b1976b317e9ed496857439c86e99253f9


---
# <a name="verify-a-gateway-connection"></a>게이트웨이 연결 확인
몇 가지 방법으로 게이트웨이 연결을 확인할 수 있습니다. 이 문서에서는 Azure 포털을 사용하고 PowerShell을 사용하여 Resource Manager 게이트웨이 연결의 상태를 확인하는 방법을 보여 줍니다.

## <a name="verify-using-powershell"></a>PowerShell을 사용하여 확인
최신 버전의 Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요. Resource Manager cmdlet을 사용하는 방법에 대한 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1-log-in-to-your-azure-account"></a>1단계: Azure 계정에 로그인
1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다.
   
        Login-AzureRmAccount
2. 계정에 대한 구독을 확인합니다.
   
        Get-AzureRmSubscription 
3. 사용할 구독을 지정합니다.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>2단계: 연결 확인
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Azure Portal을 사용하여 확인
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>다음 단계
* 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.




<!--HONumber=Dec16_HO1-->


