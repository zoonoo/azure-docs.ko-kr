---
title: "P2S 클라이언트 인증서 설치 | Azure"
description: "P2S 인증서 인증용 Mac 또는 Windows 클라이언트 인증서를 설치합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: de98201b65f5531f334aded1056f622cecb6e190
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>지점 및 사이트 간 Azure 인증서 인증 연결용 클라이언트 인증서 설치

지점 및 사이트 간 Azure 인증서 인증을 사용하여 가상 네트워크에 연결하는 모든 클라이언트에는 클라이언트 인증서가 필요합니다. 이 문서에서는 P2S를 사용하여 VNet에 연결할 때 인증에 사용되는 클라이언트 인증서를 설치합니다.

## <a name="generate"></a>클라이언트 인증서 생성 및 내보내기

엔터프라이즈 CA 솔루션을 사용하여 생성된 루트 인증서 또는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성할 수 있습니다. 단계는 [PowerShell](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)의 지침을 참조하세요. 클라이언트 인증서를 생성한 후 .pfx 파일로 내보냅니다. 내보낼 때 전체 인증서 체인을 포함해야 합니다.

## <a name="installwin"></a>Windows 클라이언트에 인증서 설치

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac 클라이언트에 인증서 설치

Mac VPN 클라이언트는 Resource Manager 배포 모델에서만 지원됩니다. 클래식 배포 모델에서는 지원되지 않습니다.

> [!NOTE]
>  IKEv2는 현재 미리 보기로 제공되고 있습니다.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 구성 단계를 계속합니다.

* [Azure 포털](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)