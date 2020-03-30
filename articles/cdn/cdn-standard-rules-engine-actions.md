---
title: Azure CDN에 대한 표준 규칙 엔진의 작업 | 마이크로 소프트 문서
description: Azure CDN(Azure CDN)에 대한 표준 규칙 엔진의 작업에 대한 참조 설명서입니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171629"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진의 작업

Azure CDN(Azure CDN)에 대한 [표준 규칙 엔진에서](cdn-standard-rules-engine.md) 규칙은 하나 이상의 일치 조건과 작업으로 구성됩니다. 이 문서에서는 Azure CDN의 표준 규칙 엔진에서 사용할 수 있는 작업에 대한 자세한 설명을 제공합니다.

규칙의 두 번째 부분은 작업입니다. 작업은 일치 조건 또는 일치 조건 집합이 식별하는 요청 유형에 적용되는 동작을 정의합니다.

## <a name="actions"></a>동작

다음 작업은 Azure CDN의 표준 규칙 엔진에서 사용할 수 있습니다. 

### <a name="cache-expiration"></a>캐시 만료

이 작업을 사용하여 규칙이 조건과 일치하는 요청에 대해 TTL(라이브) 값을 덮어씁니다.

#### <a name="required-fields"></a>Required fields

캐시 동작 |  설명              
---------------|----------------
바이패스 캐시 | 이 옵션을 선택하고 규칙이 일치하면 콘텐츠가 캐시되지 않습니다.
재정의 | 이 옵션을 선택하고 규칙이 일치하면 원본에서 반환된 TTL 값이 작업에 지정된 값으로 덮어씁니다.
누락된 경우 설정 | 이 옵션을 선택하고 규칙이 일치하면 원점에서 TTL 값이 반환되지 않은 경우 규칙은 TTL을 작업에 지정된 값으로 설정합니다.

#### <a name="additional-fields"></a>추가 필드

일 | 시간 | 분 | 초
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>캐시 키 쿼리 문자열

이 작업을 사용하여 쿼리 문자열을 기반으로 캐시 키를 수정합니다.

#### <a name="required-fields"></a>Required fields

동작 | 설명
---------|------------
포함 | 이 옵션을 선택하고 규칙이 일치하면 캐시 키가 생성될 때 매개 변수에 지정된 쿼리 문자열이 포함됩니다. 
모든 고유한 URL 캐시 | 이 옵션을 선택하고 규칙이 일치하면 각 고유 URL에는 고유한 캐시 키가 있습니다. 
제외 | 이 옵션을 선택하고 규칙이 일치하면 캐시 키가 생성될 때 매개 변수에 지정된 쿼리 문자열이 제외됩니다.
쿼리 문자열 무시 | 이 옵션을 선택하고 규칙이 일치하면 캐시 키가 생성될 때 쿼리 문자열이 고려되지 않습니다. 

### <a name="modify-request-header"></a>요청 헤더 수정

이 작업을 사용하여 원본으로 전송된 요청에 있는 헤더를 수정할 수 있습니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션을 선택하고 규칙이 일치하면 **헤더 이름에** 지정된 헤더가 지정된 값으로 요청에 추가됩니다. 헤더가 이미 있는 경우 값이 기존 값에 추가됩니다. | String
Overwrite | 이 옵션을 선택하고 규칙이 일치하면 **헤더 이름에** 지정된 헤더가 지정된 값으로 요청에 추가됩니다. 헤더가 이미 있는 경우 지정된 값은 기존 값을 덮어씁니다. | String
DELETE | 이 옵션을 선택하면 규칙이 일치하고 규칙에 지정된 헤더가 있으면 요청에서 헤더가 삭제됩니다. | String

### <a name="modify-response-header"></a>응답 헤더 수정

이 작업을 사용하여 클라이언트에 반환된 응답에 있는 헤더를 수정합니다.

#### <a name="required-fields"></a>Required fields

작업 | HTTP 헤더 이름 | 값
-------|------------------|------
추가 | 이 옵션을 선택하고 규칙이 일치하면 **헤더 이름에** 지정된 헤더가 지정된 **Value**를 사용하여 응답에 추가됩니다. 헤더가 이미 있는 경우 **값은** 기존 값에 추가됩니다. | String
Overwrite | 이 옵션을 선택하고 규칙이 일치하면 **헤더 이름에** 지정된 헤더가 지정된 **Value**를 사용하여 응답에 추가됩니다. 헤더가 이미 있는 경우 **Value는** 기존 값을 덮어씁니다. | String
DELETE | 이 옵션을 선택하면 규칙이 일치하고 규칙에 지정된 헤더가 있으면 응답에서 헤더가 삭제됩니다. | String

### <a name="url-redirect"></a>URL 리디렉션

이 작업을 사용하여 클라이언트를 새 URL로 리디렉션합니다. 

#### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
Type | 요청자로 돌아갈 응답 유형을 선택합니다: 찾음(302), 이동(301), 임시 리디렉션(307), 영구 리디렉션(308).
프로토콜 | 매치 요청, HTTP, HTTPS.
Hostname | 요청을 리디렉션할 호스트 이름을 선택합니다. 들어오는 호스트를 보존하려면 비워 둡니다.
경로 | 리디렉션에서 사용할 경로를 정의합니다. 들어오는 경로를 유지하려면 비워 둡니다.  
쿼리 문자열 | 리디렉션에 사용되는 쿼리 문자열을 정의합니다. 들어오는 쿼리 문자열을 유지하려면 비워 둡니다. 
조각 | 리디렉션에서 사용할 조각을 정의합니다. 들어오는 조각을 보존하려면 비워 둡니다. 

절대 URL을 사용하는 것이 좋습니다. 상대 URL을 사용하면 Azure CDN URL을 잘못된 경로로 리디렉션할 수 있습니다. 

### <a name="url-rewrite"></a>URL 다시 쓰기

이 작업을 사용하여 원본으로 가는 요청의 경로를 다시 작성합니다.

#### <a name="required-fields"></a>Required fields

필드 | 설명 
------|------------
소스 패턴 | 바꿀 URL 경로에서 소스 패턴을 정의합니다. 현재 소스 패턴은 접두사 기반 일치를 사용합니다. 모든 URL 경로와 일치하려면 전달**/** 슬래시() 를 소스 패턴 값으로 사용합니다.
대상 | 다시 쓰기에 사용할 대상 경로를 정의합니다. 대상 경로는 소스 패턴을 덮어씁니다.
일치하지 않는 경로 보존 | **예로**설정하면 원본 패턴 다음의 나머지 경로가 새 대상 경로에 추가됩니다. 

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 조건 일치](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
