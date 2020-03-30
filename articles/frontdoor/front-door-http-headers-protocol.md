---
title: Azure 정문에서 HTTP 헤더에 대한 프로토콜 지원 | 마이크로 소프트 문서
description: 이 문서에서는 Front Door가 지원하는 HTTP 헤더 프로토콜에 대해 설명합니다.
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471679"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure 정문에서 HTTP 헤더에 대한 프로토콜 지원
이 문서에서는 Front Door가 호출 경로의 일부와 함께 지원하는 프로토콜을 간략하게 설명합니다(이미지 참조). 다음 섹션에서는 Front Door에서 지원하는 HTTP 헤더에 대한 자세한 정보를 제공합니다.

![Azure 정문 HTTP 헤더 프로토콜][1]

>[!IMPORTANT]
>Front Door는 여기에 설명되지 않은 HTTP 헤더를 인증하지 않습니다.

## <a name="client-to-front-door"></a>클라이언트-Front Door
Front Door는 들어오는 요청에서 대부분의 헤더를 수정하지 않고 수락합니다. X-FD-* 접두사가 있는 헤더를 포함하여 일부 예약된 헤더가 전송된 경우 들어오는 요청에서 제거됩니다.

## <a name="front-door-to-backend"></a>Front Door-백 엔드

정문 에는 제한 사항으로 인해 제거되지 않는 한 들어오는 요청의 헤더가 포함됩니다. 또한 프런트 도어는 다음 헤더를 추가합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| Via |  비아: 1.1 Azure </br> Front Door는 클라이언트의 HTTP 버전을 추가하고 *Azure를* Via 헤더의 값으로 추가합니다. 이 헤더는 클라이언트의 HTTP 버전과 정문이 클라이언트와 백 엔드 간의 요청에 대한 중간 수신자임을 나타냅니다.  |
| X-Azure-클라이언트IP | X-Azure-클라이언트IP: 127.0.0.1 </br> 처리 중인 요청과 연결된 클라이언트 IP 주소를 나타냅니다. 예를 들어 프록시에서 오는 요청은 X-Forwarded-For 헤더를 추가하여 원래 호출자의 IP 주소를 나타낼 수 있습니다. |
| X-Azure 소켓 IP |  X-Azure 소켓IP: 127.0.0.1 </br> 현재 요청이 시작된 TCP 연결과 연결된 소켓 IP 주소를 나타냅니다. 요청의 클라이언트 IP 주소는 사용자가 임의로 덮어쓸 수 있으므로 소켓 IP 주소와 같지 않을 수 있습니다.|
| X-Azure-참조 |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTKANDM3YzgyY2QTMzYwYS00YTU0LTk0yzMtNWZMZA3NjQ3Nzgz3Nzgz </br> Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되며 문제 해결에 중요합니다.|
| X-Azure-요청 체인 |  X-Azure-요청 체인: 홉=1 </br> Front Door가 요청 루프를 검색하는 데 사용하는 헤더이며 사용자는 이에 대한 종속성을 가져서는 안 됩니다. |
| X-Forwarded-For | X-포워드 용: 127.0.0.1 </br> X-전달-For(XFF) HTTP 헤더 필드는 종종 HTTP 프록시 또는 로드 밸런서를 통해 웹 서버에 연결하는 클라이언트의 원래 IP 주소를 식별합니다. 기존 XFF 헤더가 있는 경우 Front Door는 클라이언트 소켓 IP를 추가하거나 클라이언트 소켓 IP를 사용하여 XFF 헤더를 추가합니다. |
| X-Forwarded-Host | X-포워드 호스트: contoso.azurefd.net </br> X-전달 호스트 HTTP 헤더 필드는 호스트 HTTP 요청 헤더에서 클라이언트가 요청한 원래 호스트를 식별하는 데 사용되는 일반적인 방법입니다. 이는 요청을 처리하는 백 엔드 서버에 대해 Front Door의 호스트 이름이 다를 수 있기 때문입니다. |
| X-Forwarded-Proto | X-포워드 프로토: http </br> X-전달-Proto HTTP 헤더 필드는 구성에 따라 Front Door가 HTTPS를 사용하여 백 엔드와 통신할 수 있기 때문에 HTTP 요청의 원래 프로토콜을 식별하는 데 자주 사용됩니다. 역방향 프록시에 대한 요청이 HTTP인 경우에도 마찬가지입니다. |
| X-FD-헬스프로브 | X-FD-HealthProbe HTTP 헤더 필드는 정문에서 상태 프로브를 식별하는 데 사용됩니다. 이 헤더가 1로 설정되면 요청은 상태 프로브입니다. X-Forwarded-Host 헤더 필드를 사용하여 특정 정문에서 엄격하게 액세스하려는 경우 사용할 수 있습니다. |

## <a name="front-door-to-client"></a>Front Door-클라이언트

백 엔드에서 정문으로 전송된 모든 헤더도 클라이언트로 전달됩니다. 다음은 정문에서 클라이언트로 전송된 헤더입니다.

| 헤더  | 예제 |
| ------------- | ------------- |
| X-Azure-참조 |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTKANDM3YzgyY2QTMzYwYS00YTU0LTk0yzMtNWZMZA3NjQ3Nzgz3Nzgz* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 이는 액세스 로그를 검색하는 데 사용되기 때문에 문제 해결에 매우 중요합니다.|

## <a name="next-steps"></a>다음 단계

- [정문 만들기](quickstart-create-front-door.md)
- [정문 작동 방식](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
