---
title: Azure Front 도어 표준/프리미엄 규칙 집합 작업 구성
description: 이 문서에서는 Azure Front 도어 규칙 집합으로 수행할 수 있는 다양 한 작업 목록을 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100047"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Azure Front 도어 표준/프리미엄 규칙 집합 작업

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure 전면 도어 [규칙 집합](concept-rule-set.md) 은 일치 조건 및 작업의 조합을 포함 하는 규칙으로 구성 됩니다. 이 문서에서는 규칙 집합에서 사용할 수 있는 작업에 대 한 자세한 설명을 제공 합니다. 작업은 일치 조건에서 식별 하는 요청 형식에 적용 되는 동작을 정의 합니다. Azure Front 도어 규칙 집합에서 규칙은 최대 5 개의 작업을 포함할 수 있습니다. 서버 변수는 모든 동작에서 지원 됩니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다음 작업은 Azure Front 도어 규칙 집합에서 사용할 수 있습니다.  

## <a name="cache-expiration"></a>캐시 만료

이 작업을 사용 하 여 규칙 일치 조건에서 지정 하는 요청에 대해 끝점의 TTL (time to live) 값을 덮어씁니다.

### <a name="required-fields"></a>Required fields

다음 설명은 이러한 캐시 동작을 선택 하 고 규칙을 일치 시킬 때 적용 됩니다.

캐시 동작 |  Description              
---------------|----------------
캐시 무시 | 콘텐츠가 캐시 되지 않습니다.
재정의 | 원본에서 반환 된 TTL 값을 작업에 지정 된 값으로 덮어씁니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용 됩니다. 캐시 제어 응답 헤더 값이 "no cache", "private", "no store" 인 경우에는 해당 작업을 적용할 수 없습니다.
누락 된 경우 설정 | 원본에서 TTL 값이 반환 되지 않는 경우 규칙은 TTL을 작업에 지정 된 값으로 설정 합니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용 됩니다. 캐시 제어 응답 헤더 값이 "no cache", "private", "no store" 인 경우에는 해당 작업을 적용할 수 없습니다.

### <a name="additional-fields"></a>추가 필드

일 | 시간 | 분 | 초
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>캐시 키 쿼리 문자열

이 작업을 사용 하 여 쿼리 문자열에 따라 캐시 키를 수정할 수 있습니다.

### <a name="required-fields"></a>Required fields

다음 설명은 이러한 동작을 선택 하 고 규칙을 일치 시킬 때 적용 됩니다.

동작 | 설명
---------|------------
포함 | 매개 변수에 지정 된 쿼리 문자열은 캐시 키가 생성 될 때 포함 됩니다. 
모든 고유한 URL 캐시 | 각 고유 URL에는 자체 캐시 키가 있습니다. 
제외 | 캐시 키가 생성 될 때 매개 변수에 지정 된 쿼리 문자열이 제외 됩니다.
쿼리 문자열 무시 | 캐시 키가 생성 될 때 쿼리 문자열이 고려 되지 않습니다. 

## <a name="modify-request-header"></a>요청 헤더 수정

원본으로 보낸 요청에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

### <a name="required-fields"></a>Required fields

다음 설명은 이러한 작업을 선택 하 고 규칙을 일치 시킬 때 적용 됩니다.

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | **헤더 이름** 에 지정 된 헤더가 지정 된 값을 사용 하 여 요청에 추가 됩니다. 헤더가 이미 있으면 값이 기존 값에 추가됩니다. | String
Overwrite | **헤더 이름** 에 지정 된 헤더가 지정 된 값을 사용 하 여 요청에 추가 됩니다. 헤더가 이미 있으면 지정한 값이 기존 값을 덮어씁니다. | String
DELETE | 규칙에 지정 된 헤더가 있으면 헤더는 요청에서 삭제 됩니다. | String

## <a name="modify-response-header"></a>응답 헤더 수정

클라이언트로 반환된 응답에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

### <a name="required-fields"></a>Required fields

다음 설명은 이러한 작업을 선택 하 고 규칙을 일치 시킬 때 적용 됩니다.

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | **헤더 이름** 에 지정 된 헤더는 지정 된 **값** 을 사용 하 여 응답에 추가 됩니다. 헤더가 이미 있으면 **값** 이 기존 값에 추가됩니다. | String
Overwrite | **헤더 이름** 에 지정 된 헤더는 지정 된 **값** 을 사용 하 여 응답에 추가 됩니다. 헤더가 이미 있으면 **값** 이 기존 값을 덮어씁니다. | String
DELETE | 규칙에 지정 된 헤더가 있는 경우 헤더는 응답에서 삭제 됩니다. | String

## <a name="url-redirect"></a>URL 리디렉션

클라이언트를 새 URL로 리디렉션하려면 다음 작업을 사용합니다. 

### <a name="required-fields"></a>Required fields

필드 | Description 
------|------------
리디렉션 유형 | 요청자에게 반환할 응답 형식(찾음(302), 이동됨(301), 임시 리디렉션(307) 및 영구 리디렉션(308))을 선택합니다.
리디렉션 프로토콜 | 요청, HTTP, HTTPS를 일치시킵니다.
대상 호스트 | 요청을 리디렉션할 호스트 이름을 선택합니다. 들어오는 호스트를 유지하려면 비워 둡니다.
대상 경로 | 리디렉션에 사용할 경로를 정의합니다. 들어오는 경로를 유지하려면 비워 둡니다.  
쿼리 문자열 | 리디렉션에 사용되는 쿼리 문자열을 정의합니다. 들어오는 쿼리 문자열을 유지하려면 비워 둡니다. 
대상 조각 | 리디렉션에 사용할 조각을 정의합니다. 들어오는 조각을 유지하려면 비워 둡니다. 

## <a name="url-rewrite"></a>URL 다시 쓰기

원본으로 라우팅하는 요청의 경로를 다시 작성하려면 다음 작업을 사용합니다.

### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
원본 패턴 | 대체할 URL 경로에서 원본 패턴을 정의 합니다. 현재 소스 패턴은 접두사 기반 일치를 사용 합니다. 모든 URL 경로를 일치 시키려면 **/** 소스 패턴 값으로 슬래시 ()를 사용 합니다.
대상 | 재작성에 사용할 대상 경로를 정의 합니다. 대상 경로는 원본 패턴을 덮어씁니다.
일치 하지 않는 경로 유지 | **예** 로 설정 된 경우 소스 패턴 뒤의 나머지 경로는 새 대상 경로에 추가 됩니다. 

## <a name="server-variable"></a>서버 변수

### <a name="supported-variables"></a>지원 되는 변수

| 변수 이름 | 설명                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | Azure Front 도어 edge에 대 한 직접 연결의 IP 주소입니다. 클라이언트에서 HTTP 프록시 또는 부하 분산 장치를 사용 하 여 요청을 보낸 경우 SocketIp의 값은 프록시 또는 부하 분산 장치의 IP 주소입니다. |
| client_ip                  | 원래 요청을 수행한 클라이언트의 IP 주소입니다. 요청에 X로 전달 된 헤더가 있는 경우 클라이언트 IP는 동일한에서 선택 됩니다. |
| client_port                | 요청을 수행한 클라이언트의 IP 포트입니다. |
| hostname                      | 클라이언트의 요청에 있는 호스트 이름입니다. |
| geo_country                     | 해당 국가/지역 코드를 통해 요청자의 원산 국가/지역을 나타냅니다. |
| http_method                | URL 요청을 만드는 데 사용 되는 메서드입니다. 예를 들어 GET 또는 POST입니다. |
| http_version               | 요청 프로토콜입니다. 일반적으로 HTTP/1.0, HTTP/1.1 또는 HTTP/2.0입니다. |
| query_string               | 요청 된 URL에서 "?" 뒤에 오는 변수/값 쌍의 목록입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* query_string 값은 *id = 123&title = fabrikam* 입니다. |
| request_scheme             | 요청 체계: http 또는 https. |
| request_uri                | 전체 원래 요청 URI (인수 포함)입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* request_uri 값은 */article.aspx? id = 123&title = fabrikam* 입니다. |
| server_port                | 요청을 수락한 서버 포트입니다. |
| ssl_protocol    | 설정 된 TLS 연결의 프로토콜입니다. |
| url_path                   | 웹 클라이언트에서 액세스 하려는 호스트의 특정 리소스를 식별 합니다. 이는 인수가 없는 요청 URI의 일부입니다. 예: 요청에서 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path 값은 */article.aspx* 입니다. |

### <a name="server-variable-format"></a>서버 변수 형식    

**형식:** {variable: offset}, {variable: offset: length}, {variable}

### <a name="supported-server-variable-actions"></a>지원 되는 서버 변수 작업

* 요청 헤더
* 응답 헤더
* 캐시 키 쿼리 문자열
* URL 다시 쓰기
* URL 리디렉션

## <a name="next-steps"></a>다음 단계

* [Azure Front 도어 Stanard/Premium 규칙 집합](concept-rule-set.md)에 대해 자세히 알아보세요.
* [규칙 집합 일치 조건](concept-rule-set-match-conditions.md)에 대해 자세히 알아보세요.
