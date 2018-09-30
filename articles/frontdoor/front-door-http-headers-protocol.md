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
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951137"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP 헤더 프로토콜 지원
이 문서에서는 아래 이미지에 설명된 것처럼 Azure Front Door Service에서 다양한 호출 경로를 사용하여 지원하는 프로토콜을 설명합니다. 아래 섹션에서는 Front Door에서 지원되는 HTTP 헤더에 대해 자세히 설명합니다.

![Azure Front Door Service HTTP 헤더 프로토콜][1]

>[!WARNING]
>Azure Front Door Service는 여기에 설명되지 않은 HTTP 헤더를 보장하지 않습니다.

## <a name="1-client-to-front-door"></a>1. 클라이언트-Front Door

다음 헤더는 Front Door를 통해 구문 분석됩니다. 그 외의 헤더에서는 Front Door가 관문 역할을 하여 응용 프로그램 백 엔드에 헤더를 전송합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> 단일 웹 페이지를 렌더링하기 위해 작성된 하나 이상의 HTTP 요청을 식별하는 GUID입니다. Front Door는 X-FD-ImpressionGuid 헤더를 사용하여 백 엔드에 GUID를 전달합니다. </br> 클라이언트가 이 헤더를 보내지 않으면 Front Door가 자동으로 새 GUID를 생성하여 백 엔드로 전송합니다. |

## <a name="2-front-door-to-backend"></a>2. Front Door-백 엔드

다음 헤더는 Front Door에서 응용 프로그램 백 엔드로 전송됩니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Front Door에서 처리하는 요청을 식별하는 ID입니다. 이 헤더 값은 문제 해결에 가장 중요한 정보입니다. 이 ID를 사용하여 액세스 로그를 검색할 수 있습니다. Front Door는 클라이언트와 백 엔드에 동일한 헤더를 전송합니다. |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>클라이언트와 Front Door 사이의 HTTP 프로토콜 버전입니다. 가능한 값은 1.1, 2.0 등입니다. |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>처리되는 요청과 연결된 "클라이언트" 인터넷 프로토콜 주소 |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>요청을 처리하는 특정 Front Door 노드입니다. 이 헤더를 백 엔드에서 로깅하면 디버깅 작업에 도움이 됩니다. 그러나 Front Door 명명 규칙, Front Door 노드 간 트래픽 분산 또는 특정 개별 노드의 가용성에 종속되지 않아야 합니다. |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>요청 처리를 시작할 때 생성되는 GUID(고유 식별자)입니다. 이 요청을 처리하는 동안 생성된 모든 액세스 로그는 동일한 GUID에 연결됩니다. |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>단일 웹 페이지를 렌더링하기 위해 작성된 하나 이상의 HTTP 요청을 식별하는 GUID입니다. 클라이언트 쪽에서 다음과 같은 방법으로 이 GUID를 지정할 수 있습니다. </br>- *X-MSEdge-IG* 요청 헤더로 전송. 예: X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>- “ig” 쿼리 문자열로 전송. 예: *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F*. 클라이언트에서 GUID를 보내지 않으면 Front Door에서 새 GUID를 생성합니다. |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> 클라이언트에서 만든 원래 요청의 URL입니다. |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>요청이 처리되는 Front Door 프로필의 식별자입니다. 다양한 응용 프로그램을 호스트하는 백 엔드에서 여러 Front Door에 사용할 수 있으며, 현재 요청에 어떤 Front Door 구성을 사용할 것인지 결정하기 위한 방법으로 사용됩니다. |
| X-FD-RequestHadClientId | Reserved |
| X-FD-ResponseHasClientId | Reserved |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>위의 X-FD-ClientIP와 동일 |

## <a name="3-front-door-to-client"></a>3. Front Door-클라이언트

다음은 Front Door에서 클라이언트로 전송되는 헤더입니다. 백 엔드에서 Front Door로 전송되는 모든 헤더는 클라이언트를 통과합니다.

| 헤더  | 예 |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>Front Door에서 처리하는 요청을 식별하는 ID입니다. 이 헤더 값은 문제 해결에 가장 중요한 정보입니다. 이 ID를 사용하여 액세스 로그를 검색할 수 있습니다. Front Door는 클라이언트와 백 엔드에 동일한 헤더를 전송합니다. |

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png