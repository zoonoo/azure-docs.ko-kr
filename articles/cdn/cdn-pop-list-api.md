---
title: Azure CDN에 대한 현재 POP IP 목록 검색 | 마이크로 소프트 문서
description: 현재 POP 목록을 검색하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299242"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Azure CDN의 현재 POP IP 목록 검색

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Azure CDN에 대한 현재 버라이존 POP IP 목록 검색

Verizon의 POP(point of presence) 서버용 IP 집합을 검색하려면 REST API를 사용할 수 있습니다. 이러한 POP 서버는 Verizon 프로필에서 Azure CDN(Content Delivery Network) 엔드포인트(**Verizon에서 Azure CDN 표준** 또는 **Verizon에서 Azure CDN 프리미엄**)와 연결된 원본 서버에 요청합니다. 이 IP 집합은 Pop에 요청할 때 클라이언트가 볼 수 있는 IP와 다른지 확인합니다. 

POP 목록을 검색하기 위한 REST API 작업의 구문은 [에지 노드 - 목록](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)을 참조합니다.

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Azure CDN에 대한 현재 Microsoft POP IP 목록 검색

Microsoft의 Azure CDN에서만 트래픽을 허용하도록 응용 프로그램을 잠그려면 백 엔드에 대한 IP ACL을 설정해야 합니다. Microsoft에서 Azure CDN에서 보낸 헤더 'X-Forwarded-Host'에 대해 허용되는 값 집합을 제한할 수도 있습니다. 이 단계는 다음과 같이 자세히 설명되어 있습니다.

백 엔드가 Microsoft의 백 엔드 IP 주소 공간 및 Azure의 인프라 서비스에서만 Azure CDN의 트래픽을 허용하도록 IP ACLing을 구성합니다. 

* 마이크로소프트의 IPv4 백 엔드 IP 공간에서 Azure CDN: 147.243.0.0/16
* 마이크로소프트의 IPv6 백 엔드 IP 공간에서 Azure CDN: 2a01:111:2050:/44

Microsoft 서비스에 대한 IP 범위 및 서비스 태그는 [여기에서](https://www.microsoft.com/download/details.aspx?id=56519) 찾을 수 있습니다.


## <a name="typical-use-case"></a>일반적인 사용 사례

보안을 위해 이 IP 목록을 사용하여 원본 서버에 대한 요청을 유효한 Verizon POP에서만 할 수 있도록 적용합니다. 예를 들어 누군가 CDN 엔드포인트의 원본 서버에 대한 호스트 이름 또는 IP 주소를 발견한 경우 원본 서버에 직접 요청을 할 수 있으므로 Azure CDN에서 제공하는 크기 조정 및 보안 기능은 바이패스합니다. 반환된 목록의 IP를 원본 서버에서 유일하게 허용된 IP로 설정하여 이 시나리오를 방지할 수 있습니다. 최신 POP 목록이 있는지 확인하려면 하루에 한 번 이상 검색합니다. 

## <a name="next-steps"></a>다음 단계

REST API에 대한 정보는 [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)를 참조하세요.
