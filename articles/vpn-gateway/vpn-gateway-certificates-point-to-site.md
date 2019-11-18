---
title: 'P2S에 대 한 인증서 생성 및 내보내기: PowerShell'
titleSuffix: Azure VPN Gateway
description: Windows 10 또는 Windows Server 2016의 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151367"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 아티클에서는 Windows 10 또는 Windows Server 2016에서 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [인증서 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 또는 [인증서 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)를 참조하세요.

Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 이 문서의 단계를 수행해야 합니다. 인증서를 생성하는 데 사용하는 PowerShell cmdlet은 운영 체제의 일부이며 다른 Windows 버전에서는 작동하지 않습니다. Windows 10 또는 Windows Server 2016 컴퓨터는 인증서 생성에만 필요합니다. 인증서를 생성한 후에는 지원되는 모든 클라이언트 운영 체제에 업로드하거나 설치할 수 있습니다.

Windows 10 또는 Windows Server 2016 컴퓨터에 액세스할 수 없는 경우 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)를 사용하여 인증서를 생성할 수 있습니다. 두 방법 중 하나를 사용하여 생성하는 인증서는 [지원되는](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) 모든 클라이언트 운영 체제에 설치할 수 있습니다.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>내보낸 클라이언트 인증서 설치

P2S 연결을 통해 VNet에 연결하는 각 클라이언트는 클라이언트 인증서를 로컬로 설치해야 합니다.

클라이언트 인증서를 설치하려면 [지점 및 사이트 간 연결에 클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 구성을 계속합니다.

* **Resource Manager** 배포 모델 단계는 [네이티브 Azure 인증서 인증을 사용하여 P2S 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요.
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.