---
title: Azure Front Door Service - HTTP 헤더 프로토콜 지원 | Microsoft Docs
description: 이 문서에서는 Front Door에서 지원하는 HTTP 헤더 프로토콜을 설명합니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038853"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP 헤더 프로토콜 지원
이 문서에서는 아래 이미지에 설명된 것처럼 Azure Front Door Service에서 다양한 호출 경로를 사용하여 지원하는 프로토콜을 설명합니다. 아래 섹션에서는 Front Door에서 지원되는 HTTP 헤더에 대해 자세히 설명합니다.

![Azure Front Door Service HTTP 헤더 프로토콜][1]

>[!WARNING]
>Azure Front Door Service는 여기에 설명되지 않은 HTTP 헤더를 보장하지 않습니다.

## <a name="1-client-to-front-door"></a>1. 클라이언트-Front Door
Front Door는 수신 요청의 헤더 대부분을 수정하지 않고 수락하지만 수신 요청에서 제거되는 일부 예약 헤더가 있습니다. 여기에는 다음과 같은 접두사가 달린 헤더가 포함됩니다.
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2. Front Door-백 엔드

Front Door는 위에 언급된 제한 사항으로 인해 제거되지 않은 수신 요청을 포함합니다. Front Door는 다음 헤더도 추가합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되므로 문제 해결에 중요합니다.|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> 이는 Front Door에서 요청 루프를 감지하는 헤더이며, 사용자는 이에 종속되지 않아야 합니다. |
| X-MS-Via |  *X-MS-Via: Azure* </br> 이는 Azure/Front Door가 클라이언트와 백엔드 간 요청의 중간 수신자였음을 나타내기 위해 Front Door에서 추가하는 것입니다. |

## <a name="3-front-door-to-client"></a>3. Front Door-클라이언트

다음은 Front Door에서 클라이언트로 전송되는 헤더입니다. 백 엔드에서 Front Door로 전송되는 모든 헤더는 클라이언트를 통과합니다.

| 헤더  | 예 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되므로 문제 해결에 중요합니다.|

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png