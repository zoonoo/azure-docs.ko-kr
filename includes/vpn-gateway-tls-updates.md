---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f2d7aba05fc01c5a4dcdb123f25242c4e4a72578
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43426431"
---
>[!NOTE]
>2018년 7월 1일부터 Azure VPN 게이트웨이에서 1.1 및 TLS 1.0에 대한 지원이 제거될 예정입니다. VPN Gateway는 TLS 1.2만 지원합니다. 지원을 유지하려면 [TLS1.2에 대한 지원을 사용하도록 업데이트](#tls1)를 참조하세요.

또한 다음 레거시 알고리즘도 2018년 7월 1일부터 TLS에서 더 이상 사용되지 않습니다.

* RC4(Rivest Cipher 4)
* DES(Data Encryption Algorithm)
* 3DES(Triple Data Encryption Algorithm)
* MD5(메시지 다이제스트 5)

### <a name="tls1"></a>Windows 7 및 Windows 8.1에서 TLS 1.2에 대한 지원을 사용하도록 설정하는 방법

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
