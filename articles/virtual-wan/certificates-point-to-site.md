---
title: Azure 가상 WAN 사용자 VPN 연결에 대 한 인증서 생성 및 내보내기 | Microsoft Docs
description: Windows 10 또는 Windows Server 2016의 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514915"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>가상 WAN 사용자 VPN 연결에 대 한 인증서 생성 및 내보내기

사용자 VPN 연결은 인증서를 사용 하 여 인증 합니다. 이 아티클에서는 Windows 10 또는 Windows Server 2016에서 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다.

Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 이 문서의 단계를 수행해야 합니다. 인증서를 생성하는 데 사용하는 PowerShell cmdlet은 운영 체제의 일부이며 다른 Windows 버전에서는 작동하지 않습니다. Windows 10 또는 Windows Server 2016 컴퓨터는 인증서 생성에만 필요합니다. 인증서를 생성한 후에는 지원되는 모든 클라이언트 운영 체제에 업로드하거나 설치할 수 있습니다.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>다음 단계

[사용자 VPN 연결에 대 한 가상 WAN 단계](virtual-wan-about.md) 계속
