---
title: 지점 및 사이트 간 클라이언트 인증서 설치
titleSuffix: Azure VPN Gateway
description: P2S 인증서 인증용 클라이언트 인증서를 설치하는 방법에 대해 알아봅니다(Windows, Mac, Linux).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: d4ab1009b38d250a39455a9e8a470cd7f1156793
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527114"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 인증서 인증 연결용 클라이언트 인증서 설치

P2S VPN 게이트웨이가 인증서 인증을 요구하도록 구성된 경우 각 클라이언트에는 로컬로 설치된 클라이언트 인증서가 있어야 합니다. 자체 서명된 루트 인증서에서 또는 엔터프라이즈 CA 솔루션을 사용하여 생성된 루트 인증서에서 클라이언트 인증서를 생성할 수 있습니다. 

클라이언트 인증서를 생성하면 일반적으로 인증서가 생성된 클라이언트 컴퓨터에 인증서가 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에서 VNet에 연결하려면 연결하는 컴퓨터에 클라이언트 인증서를 설치해야 합니다. 이는 해당 컴퓨터에서 VPN 클라이언트를 구성하는 것 외에 추가됩니다.

여러 메서드를 사용하여 자체 서명된 인증서를 생성하고 내보낼 수 있습니다. 자세한 내용은 다음 아티클을 참조하세요.

* [PowerShell](vpn-gateway-certificates-point-to-site.md)
* [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 클라이언트는 Resource Manager 배포 모델에서만 지원됩니다. 클래식 배포 모델에서는 지원되지 않습니다.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux 클라이언트 인증서는 클라이언트 구성의 일부로 클라이언트에 설치됩니다. [클라이언트 구성 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)에 대한 지점 및 사이트 간 구성 단계를 계속합니다.
