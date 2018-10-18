---
title: Azure CDN에 대한 현재 Verizon POP 목록 검색 | Microsoft Docs
description: REST API를 사용하여 현재 Verizon POP 목록을 검색하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 34bd958260056d7a0297c4d3545d97543a363480
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344795"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Azure CDN에 대한 현재 Verizon POP 목록 검색

Verizon의 POP(point of presence) 서버용 IP 집합을 검색하려면 REST API를 사용할 수 있습니다. 이러한 POP 서버는 Verizon 프로필에서 Azure CDN(Content Delivery Network) 엔드포인트(**Verizon에서 Azure CDN 표준** 또는 **Verizon에서 Azure CDN 프리미엄**)와 연결된 원본 서버에 요청합니다. 이 IP 집합은 Pop에 요청할 때 클라이언트가 볼 수 있는 IP와 다른지 확인합니다. 

POP 목록을 검색하기 위한 REST API 작업의 구문은 [에지 노드 - 목록](https://docs.microsoft.com/rest/api/cdn/edgenodes/edgenodes_list)을 참조합니다.

## <a name="typical-use-case"></a>일반적인 사용 사례

보안을 위해 이 IP 목록을 사용하여 원본 서버에 대한 요청을 유효한 Verizon POP에서만 할 수 있도록 적용합니다. 예를 들어 누군가 CDN 엔드포인트의 원본 서버에 대한 호스트 이름 또는 IP 주소를 발견한 경우 원본 서버에 직접 요청을 할 수 있으므로 Azure CDN에서 제공하는 크기 조정 및 보안 기능은 바이패스합니다. 반환된 목록의 IP를 원본 서버에서 유일하게 허용된 IP로 설정하여 이 시나리오를 방지할 수 있습니다. 최신 POP 목록이 있음을 확인하려면 하루에 한 번 이상 검색합니다. 

## <a name="next-steps"></a>다음 단계

REST API에 대한 정보는 [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)를 참조하세요.
