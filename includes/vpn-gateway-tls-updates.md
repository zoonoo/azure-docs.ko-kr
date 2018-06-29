---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336029"
---
2018년 7월 1일부터 Azure VPN 게이트웨이에서 1.1 및 TLS 1.0에 대한 지원이 제거될 예정입니다. VPN Gateway는 TLS 1.2만 지원합니다. TLS를 사용하는 Windows 7 및 Windows 8 지점-사이트 간 클라이언트에 대한 TLS 지원 및 연결을 유지 관리하려면 다음 업데이트를 설치하는 것이 좋습니다.

•   [TLS를 사용하도록 설정하는 Microsoft EAP 구현 업데이트](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [TLS 1.1 및 TLS 1.2를 WinHTTP에서 기본 보안 프로토콜로 사용하도록 업데이트](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

또한 다음 레거시 알고리즘도 2018년 7월 1일부터 TLS에서 더 이상 사용되지 않습니다.

* RC4(Rivest Cipher 4)
* DES(Data Encryption Algorithm)
* 3DES(Triple Data Encryption Algorithm)
* MD5(메시지 다이제스트 5)
