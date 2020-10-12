---
title: Azure CDN에 대 한 표준 규칙 엔진의 작업 | Microsoft Docs
description: Azure Content Delivery Network에 대 한 표준 규칙 엔진 (Azure CDN)의 동작에 대 한 참조 설명서입니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760127"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대 한 표준 규칙 엔진의 작업

Azure Content Delivery Network (Azure CDN)에 대 한 [표준 규칙 엔진](cdn-standard-rules-engine.md) 에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure CDN에 대 한 표준 규칙 엔진에서 사용할 수 있는 작업에 대해 자세히 설명 합니다.

규칙의 두 번째 부분은 동작입니다. 작업은 일치 조건 또는 일치 조건 세트에서 식별하는 요청 유형에 적용되는 동작을 정의합니다.

## <a name="actions"></a>동작

Azure CDN에 대 한 표준 규칙 엔진에서 다음 작업을 사용할 수 있습니다. 

### <a name="cache-expiration"></a>캐시 만료

이 작업을 사용 하 여 규칙 일치 조건에서 지정 하는 요청에 대해 끝점의 TTL (time to live) 값을 덮어씁니다.

#### <a name="required-fields"></a>Required fields

캐시 동작 |  Description              
---------------|----------------
캐시 무시 | 이 옵션을 선택 하 고 규칙을 일치 시킬 경우 콘텐츠가 캐시 되지 않습니다.
재정의 | 이 옵션을 선택 하 고 규칙을 일치 시킬 때 원점에서 반환 된 TTL 값을 작업에 지정 된 값으로 덮어씁니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용 됩니다. 캐시 제어 응답 헤더 값이 "no cache", "private", "no store" 인 경우 해당 작업은 적용 되지 않습니다.
누락 된 경우 설정 | 이 옵션을 선택 하 고 규칙을 일치 시킬 때 원본에서 TTL 값이 반환 되지 않은 경우 규칙은 TTL을 작업에 지정 된 값으로 설정 합니다. 이 동작은 응답을 캐시할 수 있는 경우에만 적용 됩니다. 캐시 제어 응답 헤더 값이 "no cache", "private", "no store" 인 경우 해당 작업은 적용 되지 않습니다.

#### <a name="additional-fields"></a>추가 필드

일 | 시간 | 분 | 초
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>캐시 키 쿼리 문자열

이 작업을 사용 하 여 쿼리 문자열에 따라 캐시 키를 수정할 수 있습니다.

#### <a name="required-fields"></a>Required fields

동작 | 설명
---------|------------
포함 | 이 옵션을 선택 하 고 규칙이 일치 하면 캐시 키가 생성 될 때 매개 변수에 지정 된 쿼리 문자열이 포함 됩니다. 
모든 고유한 URL 캐시 | 이 옵션을 선택 하 고 규칙을 일치 시킬 경우 각 고유 URL에는 자체 캐시 키가 있습니다. 
제외 | 이 옵션을 선택 하 고 규칙이 일치 하면 캐시 키가 생성 될 때 매개 변수에 지정 된 쿼리 문자열이 제외 됩니다.
쿼리 문자열 무시 | 이 옵션을 선택 하 고 규칙을 일치 시킬 경우 캐시 키가 생성 될 때 쿼리 문자열이 고려 되지 않습니다. 

### <a name="modify-request-header"></a>요청 헤더 수정

원본으로 보낸 요청에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션이 선택되고 규칙이 일치하면 지정된 값을 사용하여 **헤더 이름**에 지정된 헤더가 요청에 추가됩니다. 헤더가 이미 있으면 값이 기존 값에 추가됩니다. | String
Overwrite | 이 옵션이 선택되고 규칙이 일치하면 지정된 값을 사용하여 **헤더 이름**에 지정된 헤더가 요청에 추가됩니다. 헤더가 이미 있으면 지정한 값이 기존 값을 덮어씁니다. | String
DELETE | 이 옵션이 선택되고 규칙이 일치하고 규칙에 지정된 헤더가 있으면 요청에서 헤더가 삭제됩니다. | String

### <a name="modify-response-header"></a>응답 헤더 수정

클라이언트로 반환된 응답에 있는 헤더를 수정하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션이 선택되고 규칙이 일치하면 지정된 **값**을 사용하여 **헤더 이름**에 지정된 헤더가 응답에 추가됩니다. 헤더가 이미 있으면 **값**이 기존 값에 추가됩니다. | String
Overwrite | 이 옵션이 선택되고 규칙이 일치하면 지정된 **값**을 사용하여 **헤더 이름**에 지정된 헤더가 응답에 추가됩니다. 헤더가 이미 있으면 **값**이 기존 값을 덮어씁니다. | String
DELETE | 이 옵션이 선택되고 규칙이 일치하고 규칙에 지정된 헤더가 있으면 응답에서 헤더가 삭제됩니다. | String

### <a name="url-redirect"></a>URL 리디렉션

클라이언트를 새 URL로 리디렉션하려면 다음 작업을 사용합니다. 

#### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
Type | 요청자에게 반환할 응답 형식(찾음(302), 이동됨(301), 임시 리디렉션(307) 및 영구 리디렉션(308))을 선택합니다.
프로토콜 | 요청, HTTP, HTTPS를 일치시킵니다.
Hostname | 요청을 리디렉션할 호스트 이름을 선택합니다. 들어오는 호스트를 유지하려면 비워 둡니다.
경로 | 리디렉션에 사용할 경로를 정의합니다. 들어오는 경로를 유지하려면 비워 둡니다.  
쿼리 문자열 | 리디렉션에 사용되는 쿼리 문자열을 정의합니다. 들어오는 쿼리 문자열을 유지하려면 비워 둡니다. 
Fragment | 리디렉션에 사용할 조각을 정의합니다. 들어오는 조각을 유지하려면 비워 둡니다. 

절대 URL을 사용 하는 것이 좋습니다. 상대 URL을 사용 하면 Azure CDN Url을 잘못 된 경로로 리디렉션할 수 있습니다. 

### <a name="url-rewrite"></a>URL 다시 쓰기

원본으로 라우팅하는 요청의 경로를 다시 작성하려면 다음 작업을 사용합니다.

#### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
원본 패턴 | 대체할 URL 경로에서 원본 패턴을 정의 합니다. 현재 소스 패턴은 접두사 기반 일치를 사용 합니다. 모든 URL 경로를 일치 시키려면 **/** 소스 패턴 값으로 슬래시 ()를 사용 합니다.
대상 | 재작성에 사용할 대상 경로를 정의 합니다. 대상 경로는 원본 패턴을 덮어씁니다.
일치 하지 않는 경로 유지 | **예**로 설정 된 경우 소스 패턴 뒤의 나머지 경로는 새 대상 경로에 추가 됩니다. 

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
