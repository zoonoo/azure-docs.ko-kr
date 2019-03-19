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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726331"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP 헤더 프로토콜 지원
이 문서에서는 아래 이미지에 설명된 것처럼 Azure Front Door Service에서 다양한 호출 경로를 사용하여 지원하는 프로토콜을 설명합니다. 아래 섹션에서는 Front Door에서 지원되는 HTTP 헤더에 대해 자세히 설명합니다.

![Azure Front Door Service HTTP 헤더 프로토콜][1]

>[!WARNING]
>Azure Front Door Service는 여기에 설명되지 않은 HTTP 헤더를 보장하지 않습니다.

## <a name="1-client-to-front-door"></a>1. 클라이언트-Front Door
Front Door는 수신 요청의 헤더 대부분을 수정하지 않고 수락하지만 수신 요청에서 제거되는 일부 예약 헤더가 있습니다. 여기에는 다음과 같은 접두사가 달린 헤더가 포함됩니다.
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Front Door-백 엔드

Front Door는 위에 언급된 제한 사항으로 인해 제거되지 않은 수신 요청을 포함합니다. Front Door는 다음 헤더도 추가합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> 첫 번째 관문 값으로 'Azure' 뒤에 헤더를 통해 클라이언트의 HTTP 버전을 추가 합니다. 클라이언트의 HTTP 버전을 나타내는 추가 되는이 하 고 해당 Azure 프런트 도어 클라이언트와 백 엔드 요청에 대 한 중간에서 수신자가 키를 누릅니다.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> 처리 중인 요청과 연결 된 "클라이언트" 인터넷 프로토콜 주소를 나타냅니다. 예를 들어, 프록시에서 들어오는 요청이를 원래 호출자의 IP 주소를 나타내는 X-전달 기능에 대 한 헤더를 추가할 수 있습니다. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> 현재 요청에서 발생 한 TCP 연결과 연결 된 소켓 인터넷 프로토콜 주소를 나타냅니다. 최종 사용자가 임의로 덮어쓸 수 있으므로 클라이언트 IP 주소는 요청을 해당 소켓 IP 주소와 같고 수 없습니다.|
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되므로 문제 해결에 중요합니다.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: 홉 = 1* </br> 이는 Front Door에서 요청 루프를 감지하는 헤더이며, 사용자는 이에 종속되지 않아야 합니다. |
| X-Forwarded-For | *X-전달 기능에 대 한 합니다. 127.0.0.1* </br> X-Forwarded-For (XFF) HTTP 헤더 필드는 HTTP 프록시 또는 부하 분산 장치를 통해 웹 서버에 연결 하는 클라이언트의 원래 IP 주소를 식별 하기 위한 일반적인 메서드입니다. 그 외에 클라이언트 소켓 IP를 추가 하는 첫 번째 관문을 기존 XFF 헤더 되었으면 클라이언트 소켓 IP 사용 하 여 XFF 헤더를 추가 합니다. |
| X 전달 호스트 | *X 전달 호스트: contoso.azurefd.net* </br> X 전달 호스트 HTTP 헤더 필드는 요청을 처리 하는 백 엔드 서버에 대 한 첫 번째 관문에서 호스트 이름을 다 하므로 호스트 HTTP 요청 헤더에서 클라이언트가 요청한 원본 호스트를 식별 하기 위한 일반적인 메서드입니다. |
| X-Forwarded-Proto | *X 전달 프로토콜: http* </br> X 전달 Proto HTTP 헤더 필드는 구성에 따라 프런트 도어 역방향 프록시 요청이 HTTP 경우에 HTTPS를 사용 하 여 백 엔드를 사용 하 여 통신 수 있으므로 HTTP 요청의 원래 프로토콜을 식별 하는 데는 일반적인 메서드입니다. |

## <a name="3-front-door-to-client"></a>3. Front Door-클라이언트

다음은 Front Door에서 클라이언트로 전송되는 헤더입니다. 백 엔드에서 Front Door로 전송되는 모든 헤더는 클라이언트를 통과합니다.

| 헤더  | 예 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되므로 문제 해결에 중요합니다.|

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png