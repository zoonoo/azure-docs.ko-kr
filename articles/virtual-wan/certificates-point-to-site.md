---
title: 사용자 VPN 연결에 대한 인증서 생성 및 내보내기 | Azure Virtual WAN
description: PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, Virtual WAN 사용자 VPN(지점 및 사이트 간) 연결을 위한 클라이언트 인증서를 생성하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: d126f68ed1eebae9dda7ad957748cb3258b5ad89
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165302"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>사용자 VPN 연결에 대한 인증서 생성 및 내보내기

사용자 VPN(지점 및 사이트 간) 연결은 인증서를 사용하여 인증을 합니다. 이 아티클에서는 Windows 10 또는 Windows Server 2016에서 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다.

Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 이 문서의 단계를 수행해야 합니다. 인증서를 생성하는 데 사용하는 PowerShell cmdlet은 운영 체제의 일부이며 다른 Windows 버전에서는 작동하지 않습니다. Windows 10 또는 Windows Server 2016 컴퓨터는 인증서 생성에만 필요합니다. 인증서를 생성한 후에는 지원되는 모든 클라이언트 운영 체제에 업로드하거나 설치할 수 있습니다.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>다음 단계

[사용자 VPN 연결에 대한 Virtual WAN 단계](virtual-wan-about.md)로 계속 사용
