---
title: '지점 및 사이트 간 인증서 생성 및 내보내기: Linux: CLI'
description: Linux(strongSwan) CLI를 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984605"
---
# <a name="generate-and-export-certificates"></a>인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 문서에서는 Linux CLI 및 strongSwan을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [Powershell](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 문서를 참조하세요. CLI 대신 GUI를 사용하여 strongSwan을 설치하는 방법에 대한 자세한 내용은 [클라이언트 구성](point-to-site-vpn-client-configuration-azure-cert.md#install) 문서의 단계를 참조하세요.

## <a name="install-strongswan"></a>strongSwan 설치

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>인증서 생성 및 내보내기

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)에 대한 지점 및 사이트 간 구성을 계속합니다.
