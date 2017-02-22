---
title: "로컬 네트워크 게이트웨이 IP 주소 접두사 및 게이트웨이 IP 수정 | Microsoft Docs"
description: "이 문서는 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 변경하는 방법을 안내합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 2459f106e90616b05062224be82a06ef20fbc7b4


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정
때로는 로컬 네트워크 게이트웨이 AddressPrefix 또는 GatewayIPAddress에 대한 설정을 변경합니다. 아래 지침을 따라 로컬 네트워크 게이트웨이 설정을 수정할 수 있습니다. 또한 Azure 포털에서 이러한 설정을 수정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
최신 버전의 Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요.

## <a name="to-modify-ip-address-prefixes"></a>IP 주소 접두사를 수정하려면
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>게이트웨이 IP 주소를 수정하려면
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>다음 단계
게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.




<!--HONumber=Feb17_HO3-->


