---
title: Azure CDN 규칙 엔진 기능 | Microsoft Docs
description: Azure CDN 규칙 엔진 기능에 대한 참조 설명서입니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 205a8dae55394a82a60f54ed32bad95324a59517
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996932"
---
# <a name="azure-cdn-rules-engine-features"></a>Azure CDN 규칙 엔진 기능
이 문서에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-rules-engine.md)에 사용할 수 있는 기능에 대해 자세히 설명합니다.

규칙의 세 번째 부분은 기능을 다루고 있습니다. 기능은 일치 조건 집합으로 식별되는 요청 유형에 적용할 작업 유형을 정의합니다.

## <a name="access-features"></a>액세스 기능

이러한 기능은 콘텐츠에 대한 액세스를 제어하도록 설계되었습니다.

이름 | 용도
-----|--------
[액세스 거부(403)](#deny-access-403) | 모든 요청이 403 사용 권한 없음 응답으로 거부되는지 여부를 결정합니다.
[토큰 인증](#token-auth) | 요청에 토큰 기반 인증을 적용할지 여부를 결정합니다.
[토큰 인증 거부 코드](#token-auth-denial-code) | 토큰 기반 인증에 따라 요청이 거부되는 경우 사용자에게 반환할 응답 유형을 결정합니다.
[토큰 인증 URL 대/소문자 무시](#token-auth-ignore-url-case) | 토큰 기반 인증에서 URL 비교 시 대/소문자를 구분할지 결정합니다.
[토큰 인증 매개 변수](#token-auth-parameter) | 토큰 기반 인증 쿼리 문자열 매개 변수 이름을 바꿔야 하는지 여부를 결정합니다.


## <a name="caching-features"></a>캐싱 기능

이러한 기능은 콘텐츠가 캐시되는 시기와 방식을 사용자 지정하기 위해 설계되었습니다.

이름 | 용도
-----|--------
[대역폭 매개 변수](#bandwidth-parameters) | 대역폭 제한 매개 변수(예: ec_rate 및 ec_prebuf)를 활성화할지 여부를 결정합니다.
[대역폭 제한](#bandwidth-throttling) | POP(상호 접속 위치)에서 제공하는 응답에 대한 대역폭을 제한합니다.
[바이패스 캐시](#bypass-cache) | 요청이 캐싱을 무시할지 여부를 결정합니다.
[Cache-Control 헤더 처리](#cache-control-header-treatment) | 외부 Max-Age 기능이 활성 상태일 때 POP에 의한 `Cache-Control` 헤더의 생성을 제어합니다.
[Cache-Key 쿼리 문자열](#cache-key-query-string) | cache-key에서 요청과 관련된 쿼리 문자열 매개 변수를 포함할지 또는 제외할지 여부를 결정합니다.
[Cache-Key 다시 쓰기](#cache-key-rewrite) | 요청과 관련된 cache-key를 다시 씁니다.
[전체 캐시 채우기](#complete-cache-fill) | 요청 결과, POP에서 캐시가 부분적으로 누락된 경우 수행할 작업을 결정합니다.
[압축 파일 형식](#compress-file-types) | 서버에서 압축할 파일 형식을 정의합니다.
[기본 내부 Max-Age](#default-internal-max-age) | POP에서 원본 서버 캐시 유효성 재검사를 위한 기본 max-age 간격을 결정합니다.
[Expires 헤더 처리](#expires-header-treatment) | 외부 Max-Age 기능이 활성 상태일 때 POP에 의한 `Expires` 헤더의 생성을 제어합니다.
[외부 Max-Age](#external-max-age) | 브라우저에서 POP 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다.
[강제 내부 Max-Age](#force-internal-max-age) | POP에서 원본 서버 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다.
[H.264 지원(HTTP 점진적 다운로드)](#h264-support-http-progressive-download) | 콘텐츠를 스트리밍하는 데 사용할 수 있는 H.264 파일 형식의 유형을 결정합니다.
[no-cache 요청 부여](#honor-no-cache-request) | HTTP 클라이언트의 no-cache 요청을 원본 서버에 전달할지 여부를 결정합니다.
[원본 no-cache 무시](#ignore-origin-no-cache) | CDN이 원본 서버에서 제공되는 특정 지시문을 무시할지 여부를 결정합니다.
[적절하지 않은 범위 무시](#ignore-unsatisfiable-ranges) | 요청에서 416 요청한 범위가 적절하지 않음 상태 코드를 생성하는 경우 클라이언트로 반환할 응답을 결정합니다.
[내부 Max-Stale](#internal-max-stale) | POP가 원본 서버로 캐시된 자산의 유효성 재검사를 할 수 없는 경우 POP에서 캐시된 자산이 정상 만료 시간을 지나 얼마나 오래 제공될 수 있는지를 제어합니다.
[부분 캐시 공유](#partial-cache-sharing) | 요청에서 부분적으로 캐시된 콘텐츠를 생성할 수 있는지 여부를 결정합니다.
[캐시된 콘텐츠 사전 유효성 검사](#prevalidate-cached-content) | TTL이 만료되기 전에 캐시된 콘텐츠 유효성 재검사를 미리 수행할 수 있는지 여부를 결정합니다.
[0바이트 캐시 파일 새로 고침](#refresh-zero-byte-cache-files) | 0바이트 캐시 자산에 대한 HTTP 클라이언트 요청이 POP에 의해 처리되는 방식을 결정합니다.
[캐시 가능한 상태 코드 집합](#set-cacheable-status-codes) | 캐시된 콘텐츠가 발생할 수 있는 상태 코드 집합을 정의합니다.
[오류 시 오래된 콘텐츠 배달](#stale-content-delivery-on-error) | 캐시 유효성 재검사 중에 오류가 발생하거나 고객 원본 서버에서 요청된 콘텐츠를 검색할 때 만료되고 캐시된 콘텐츠를 배달할지 여부를 결정합니다.
[유효성 재검사 중 기한 경과](#stale-while-revalidate) | 유효성 재검사를 수행하는 동안 POP가 오래된 클라이언트를 요청자에게 제공하도록 하여 성능을 개선합니다.

## <a name="comment-feature"></a>주석 기능

이 기능은 규칙 내에 추가 정보를 제공하도록 설계되었습니다.

이름 | 용도
-----|--------
[Comment](#comment) | 규칙 내에 메모를 추가하도록 허용합니다.
 
## <a name="header-features"></a>헤더 기능

이러한 기능은 요청자 또는 응답에서 헤더를 추가, 수정 또는 삭제하도록 설계되었습니다.

이름 | 용도
-----|--------
[Age 응답 헤더](#age-response-header) | 요청자에게 보내는 응답에 Age 응답 헤더를 포함할지 여부를 결정합니다.
[디버그 캐시 응답 헤더](#debug-cache-response-headers) | 응답에 요청된 자산에 대한 캐시 정책 정보를 제공하는 X-EC-Debug 응답 헤더를 포함할 수 있는지 여부를 결정합니다.
[클라이언트 요청 헤더 수정](#modify-client-request-header) | 요청에서 헤더를 덮어쓰기, 추가 또는 삭제합니다.
[클라이언트 응답 헤더 수정](#modify-client-response-header) | 응답에서 헤더를 덮어쓰기, 추가 또는 삭제합니다.
[클라이언트 IP 사용자 지정 헤더 설정](#set-client-ip-custom-header) | 클라이언트를 요청하는 IP 주소가 사용자 지정 요청 헤더로 요청에 추가되도록 합니다.


## <a name="logging-features"></a>로깅 기능

이러한 기능은 원시 로그 파일에 저장된 데이터를 사용자 지정하도록 설계되었습니다.

이름 | 용도
-----|--------
[사용자 지정 로그 필드 1](#custom-log-field-1) | 원시 로그 파일의 사용자 지정 로그 필드에 할당할 콘텐츠와 형식을 결정합니다.
[로그 쿼리 문자열](#log-query-string) | 액세스 로그에 쿼리 문자열을 URL과 함께 저장할지 여부를 결정합니다.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>원본 기능

이러한 기능은 CDN이 원본 서버와 통신하는 방법을 제어하도록 설계되었습니다.

이름 | 용도
-----|--------
[최대 연결 유지 요청](#maximum-keep-alive-requests) | 연결이 닫히기 전에 연결을 유지할 최대 요청 수를 정의합니다.
[프록시 특별 헤더](#proxy-special-headers) | POP에서 원본 서버로 전달할 CDN 특정 요청 헤더의 집합을 정의합니다.


## <a name="specialty-features"></a>전문 기능

이러한 기능은 고급 사용자용 고급 기능을 제공합니다.

이름 | 용도
-----|--------
[캐시 가능한 HTTP 메서드](#cacheable-http-methods) | 네트워크에서 캐시할 수 있는 추가 HTTP 메서드 집합을 결정합니다.
[캐시 가능한 요청 본문 크기](#cacheable-request-body-size) | POST 응답을 캐시할 수 있는지 여부를 결정하는 임계값을 정의합니다.
[User 변수](#user-variable) | 내부 전용입니다.

 
## <a name="url-features"></a>URL 기능

이러한 기능을 통해 요청을 다른 URL로 리디렉션하거나 다시 작성할 수 있습니다.

이름 | 용도
-----|--------
[리디렉션 추적](#follow-redirects) | 고객 원본 서버에서 반환된 Location 헤더에 정의된 호스트 이름으로 요청을 리디렉션할 수 있는지 여부를 결정합니다.
[URL 리디렉션](#url-redirect) | Location 헤더를 통해 요청을 리디렉션합니다.
[URL 다시 쓰기](#url-rewrite)  | 요청 URL을 다시 씁니다.



## <a name="azure-cdn-rules-engine-features-reference"></a>Azure CDN 규칙 엔진 기능 참조

---
### <a name="age-response-header"></a>Age 응답 헤더
**목적은**: 요청자에게 보내는 응답에 Age 응답 헤더를 포함할지 여부를 결정합니다.

값|결과
--|--
사용하도록 설정됨 | 요청자에게 보내는 응답에 Age 응답 헤더를 포함합니다.
비활성화됨 | 요청자에게 보내는 응답에서 Age 응답 헤더를 제외합니다.

**기본 동작**: 사용하지 않습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

<br>

---
### <a name="bandwidth-parameters"></a>대역폭 매개 변수
**목적:** 대역폭 제한 매개 변수 (예: ec_rate 및 ec_prebuf) 여부를 결정 합니다.

대역폭 제한 매개 변수는 클라이언트 요청에 대한 데이터 전송 속도를 사용자 지정 속도로 제한하는지 여부를 결정합니다.

값|결과
--|--
사용하도록 설정됨|POP에서 대역폭 제한 요청을 따르도록 허용합니다.
비활성화됨|POP에서 대역폭 제한 매개 변수를 무시하도록 합니다. 요청된 콘텐츠는 대역폭 제한 없이 정상적으로 처리됩니다.

**기본 동작:** 사용 하도록 설정 합니다.
 
[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>대역폭 제한
**목적:** Pop에서 제공 하는 응답에 대 한 대역폭을 제한 합니다.

대역폭 제한을 제대로 설정하려면 다음 옵션을 모두 정의해야 합니다.

옵션|설명
--|--
초당 킬로바이트|이 옵션을 응답을 전달하는 데 사용할 수 있는 최대 대역폭(Kbps)으로 설정합니다.
Prebuf 초|이 옵션을 대역폭이 제한될 때까지 POP가 대기하는 시간(초)으로 설정합니다. 대역폭 제한이 없는 이 기간은 미디어 플레이어에서 대역폭 제한에 따른 스터터링 또는 버퍼링 문제가 발생하지 않도록 하기 위한 것입니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>바이패스 캐시
**목적:** 요청이 캐싱을 무시할지 여부를 결정 합니다.

값|결과
--|--
사용하도록 설정됨|이전에 POP에서 콘텐츠를 캐시한 경우에도 모든 요청을 원본 서버로 전달하도록 합니다.
비활성화됨|POP에서 응답 헤더에 정의된 캐시 정책에 따라 자산을 캐시하도록 합니다.

**기본 동작:**

- **HTTP Large:** 사용 안 함

<!---
- **ADN:** Enabled

--->

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>캐시 가능한 HTTP 메서드
**목적:** 네트워크에서 캐시할 수 있는 추가 HTTP 메서드 집합을 결정 합니다.

주요 정보:

- 이 기능은 GET 응답을 항상 캐시해야 한다고 가정합니다. 따라서 이 기능을 설정할 때 GET HTTP 메서드를 포함하지 않아야 합니다.
- 이 기능은 POST HTTP 메서드만 지원합니다. 이 기능을 `POST`로 설정하여 POST 응답 캐싱을 활성화합니다.
- 기본적으로 본문이 14Kb 미만인 요청만 캐시됩니다. 최대 요청 본문 크기를 설정 하는 캐시 가능한 요청 본문 크기 기능을 사용 합니다.

**기본 동작:** GET 응답만 캐시 됩니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>캐시 가능한 요청 본문 크기
**목적:** POST 응답을 캐시할 수 있는지 여부를 결정 하는 임계값을 정의 합니다.

이 임계값은 최대 요청 본문 크기를 지정하여 결정됩니다. 요청 본문이 더 큰 요청은 캐시되지 않습니다.

주요 정보:

- POST 응답이 캐싱에 적합한 경우에만 이 기능을 적용할 수 있습니다. 캐시 가능한 HTTP 메서드 기능을 사용 하 여 POST 요청 캐싱을 사용 하도록 설정 합니다.
- 요청 본문에 대한 고려 사항은 다음과 같습니다.
    - x-www-form-urlencoded 값
    - 고유한 cache-key 보장
- 최대 요청 본문 크기를 크게 정의하면 데이터 전달 성능에 영향을 줄 수 있습니다.
    - **권장된 값:** 14kb
    - **최소값:** 1kb

**기본 동작:** 14kb

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Cache-Control 헤더 처리
**목적:** 생성을 제어 합니다 `Cache-Control` 외부 Max-age 기능이 활성 상태일 때 POP 헤더입니다.

이 유형의 구성을 획득하는 가장 쉬운 방법은 동일한 문에 외부 Max-Age와 Cache-Control 헤더 처리 기능을 배치하는 것입니다.

값|결과
--|--
덮어쓰기|다음과 같은 작업이 수행되도록 합니다.<br/> -기존의 합니다 `Cache-Control` 원본 서버에서 생성 된 헤더입니다. <br/>-추가 합니다 `Cache-Control` 헤더 응답에 외부 Max-age 기능으로 생성 합니다.
통과|있는지 확인 합니다 `Cache-Control` 외부 Max-age 기능으로 생성 한 헤더를 응답에 절대 추가 합니다. <br/> 원본 서버에서 생성 되는 경우는 `Cache-Control` 헤더를 통해 전달 될 최종 사용자에 게 합니다. <br/> 원본 서버를 생성 하지 않는 경우는 `Cache-Control` 헤더를 다음이 옵션은 응답 헤더에 포함 되어 있지 않을 `Cache-Control` 헤더입니다.
없는 경우 추가|경우는 `Cache-Control` 헤더는 원본 서버에서 수신 하지 않은 다음이 옵션을 추가 합니다 `Cache-Control` 외부 Max-age 기능으로 생성 된 헤더입니다. 이 옵션은 모든 자산 할당 되어 있는지 확인 하는 데는 `Cache-Control` 헤더입니다.
제거| 이 옵션을 `Cache-Control` 헤더 헤더 응답에 포함 되지 않습니다. 경우는 `Cache-Control` 헤더가 이미 할당 된 다음 헤더 응답에서 제거 됩니다.

**기본 동작:** 덮어씁니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cache-Key 쿼리 문자열
**목적:** cache-key에 포함 되어 요청과 관련 된 쿼리 문자열 매개 변수를 제외 하는지 여부를 결정 합니다.

주요 정보:

- 하나 이상의 쿼리 문자열 매개 변수 이름을 지정하고, 각 매개 변수 이름은 단일 공백으로 구분합니다.
- 이 기능은 cache-key에서 쿼리 문자열 매개 변수를 포함할지 또는 제외할지 여부를 결정합니다. 다음 표에 각 옵션에 대한 추가 정보가 제공됩니다.

종류|설명
--|--
 포함|  지정된 매개 변수마다 cache-key에 포함되어야 함을 나타냅니다. 이 기능에 정의된 쿼리 문자열 매개 변수의 고유한 값을 포함하는 요청마다 고유한 cache-key가 생성됩니다. 
 모두 포함  |고유한 쿼리 문자열을 포함하는 자산에 대한 요청마다 고유한 cache-key가 생성됨을 나타냅니다. 이러한 유형의 구성은 일반적으로 캐시 적중률이 낮을 수 있으므로 권장되지 않습니다. 캐시 적중 수가 적으면 원래 서버가 더 많은 요청을 처리해야 하기 때문에 로드가 증가합니다. 이 구성에는 "고유-cache" Query-string Caching 페이지에서로 알려진 캐싱 동작을 복제 합니다. 
 제외 | 지정된 매개 변수만 cache-key에서 제외됨을 나타냅니다. 다른 모든 쿼리 문자열 매개 변수는 cache-key에 포함됩니다. 
 모두 제외  |모든 쿼리 문자열 매개 변수가 cache-key에서 제외됨을 나타냅니다. 이 구성에는 "표준-캐시" 기본 캐싱 동작 합니다 Query-string Caching 페이지에서 중복 됩니다.  

규칙 엔진을 사용하면 쿼리 문자열 캐싱이 구현되는 방식을 사용자 지정할 수 있습니다. 예를 들어 특정 위치 또는 파일 형식에서만 쿼리 문자열 캐싱을 수행하도록 지정할 수 있습니다.

"No-cache" 쿼리 문자열 캐싱 Query-string Caching 페이지에서 동작을 복제 하려면 URL 쿼리 와일드 카드 일치 조건과 Bypass Cache 기능이 포함 된 규칙을 만듭니다. URL 쿼리 와일드카드 일치 조건은 별표(*)로 설정합니다.

>[!IMPORTANT] 
> 이 계정의 모든 경로에 대해 토큰 권한 부여를 사용하도록 설정하면 표준 캐시 모드만 쿼리 문자열 캐시에 사용할 수 있습니다. 자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](cdn-query-string-premium.md)를 참조하세요.

#### <a name="sample-scenarios"></a>샘플 시나리오

이 기능에 대한 다음 샘플 사용은 샘플 요청 및 기본 캐시 키를 제공합니다.

- **샘플 요청:** http://wpc.0001.&lt; 도메인&gt;/800001/Origin/folder/asset.htm?sessionid=1234 및 언어 & userid = = 01
- **기본 cache-key:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>포함

샘플 구성:

- **형식:** 포함
- **매개 변수:** 언어

이러한 유형의 구성은 다음과 같은 cache-key 쿼리 문자열 매개 변수를 생성합니다.

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>모두 포함

샘플 구성:

- **형식:** 모두 포함

이러한 유형의 구성은 다음과 같은 cache-key 쿼리 문자열 매개 변수를 생성합니다.

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>제외

샘플 구성:

- **형식:** 제외
- **매개 변수:** sessionid userid

이러한 유형의 구성은 다음과 같은 cache-key 쿼리 문자열 매개 변수를 생성합니다.

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>모두 제외

샘플 구성:

- **형식:** 모두 제외

이러한 유형의 구성은 다음과 같은 cache-key 쿼리 문자열 매개 변수를 생성합니다.

    /800001/Origin/folder/asset.htm

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Cache-Key 다시 쓰기
**목적:** 요청과 관련 된 cache-key를 다시 작성 합니다.

cache-key는 캐싱을 위해 자산을 식별하는 상대 경로입니다. 즉 서버에서 cache-key에 정의된 경로에 따라 자산의 캐시된 버전을 확인합니다.

다음 두 옵션을 모두 정의하여 이 기능을 구성합니다.

옵션|설명
--|--
원래 경로| cache-key를 다시 쓰는 요청의 형식에 대한 상대 경로를 정의합니다. 기본 원본 경로 선택 하 고 다음 정규식 패턴을 정의 하 여 상대 경로 정의할 수 있습니다.
새 경로|새 cache-key에 대한 상대 경로를 정의합니다. 상대 경로는 기본 경로를 선택한 다음 정규식 패턴을 정의함으로써 정의할 수 있습니다. 이 상대 경로 사용 하 여 동적으로 생성할 수 있습니다 [HTTP 변수](cdn-http-variables.md)합니다.

**기본 동작:** 요청의 cache-key는 요청 URI에 의해 결정 됩니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>메모
**목적:** 메모를 규칙에 추가할 수 있습니다.

이 기능의 한 가지 용도는 규칙의 일반 목적 또는 특정 일치 조건이나 기능을 규칙에 추가한 이유에 대한 추가 정보를 제공하는 것입니다.

주요 정보:

- 최대 150자를 지정할 수 있습니다.
- 영숫자 문자만 사용합니다.
- 이 기능은 규칙의 동작에 영향을 주지 않습니다. 단지 나중에 참조할 수 있도록 정보를 제공하거나 규칙 문제를 해결할 때 도움이 될 수 있는 영역을 제공하기 위한 것입니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>전체 캐시 채우기
**목적:** 요청이 POP에서 부분 캐시 누락 발생 하는 경우 수행할 작업을 결정 합니다.

부분 캐시 누락은 POP에 완전히 다운로드되지 않은 자산의 캐시 상태를 설명합니다. 자산을 POP에 부분적으로만 캐시한 경우 해당 자산에 대한 다음 요청이 원본 서버로 다시 전달됩니다.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
부분 캐시 누락은 일반적으로 사용자가 다운로드를 중단한 후에 발생하거나 HTTP 범위 요청을 사용하여 단독으로 요청된 자산에 대해 발생합니다. 이 기능은 일반적으로 처음부터 끝까지 다운로드되지 않는 대용량 자산(예: 동영상)에 가장 유용합니다. 따라서 이 기능은 기본적으로 HTTP Large 플랫폼에서 사용하도록 설정되며, 다른 모든 플랫폼에서는 사용할 수 없습니다.

HTTP Large 플랫폼의 기본 구성을 그대로 유지하세요. 고객 원본 서버의 부하를 줄이고 고객이 콘텐츠를 다운로드하는 속도를 높이기 때문입니다.

값|결과
--|--
사용하도록 설정됨|기본 동작을 복원합니다. 기본 동작은 POP에서 원본 서버에 있는 자산의 백그라운드 페치를 시작하도록 하는 것입니다. 그런 다음 자산이 POP의 로컬 캐시에 저장됩니다.
비활성화됨|POP에서 자산에 대한 백그라운드 페치를 수행하지 못하도록 합니다. 그 결과 해당 지역의 해당 자산에 대한 다음 요청으로 인해 POP에서 고객 원본 서버의 해당 자산을 요청하게 됩니다.

**기본 동작:** 사용 하도록 설정 합니다.

#### <a name="compatibility"></a>호환성
캐시 설정을 추적하는 방식으로 인해 이 기능은 다음 일치 조건과 연결할 수 없습니다. 
- AS 숫자
- 클라이언트 IP 주소
- 쿠키 매개 변수
- 쿠키 매개 변수 Regex
- 국가
- 장치
- 에지 Cname
- 참조 도메인
- 요청 헤더 리터럴
- 요청 헤더 Regex
- 요청 헤더 와일드카드
- 요청 메서드
- 요청 스키마
- URL 쿼리 리터럴
- URL 쿼리 Regex
- URL 쿼리 와일드카드
- URL 쿼리 매개 변수

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>압축 파일 형식
**목적:** 서버에서 압축 된 파일에 대 한 파일 형식을 정의 합니다.

파일 형식은 인터넷 미디어 유형(예: Content-Type)을 사용하여 지정할 수 있습니다. 인터넷 미디어 유형은 서버에서 특정 자산의 파일 형식을 식별할 수 있는 플랫폼 독립적 메타데이터입니다. 일반적인 인터넷 미디어 유형 목록은 다음과 같습니다.

인터넷 미디어 유형|설명
--|--
텍스트/일반|일반 텍스트 파일
텍스트/html| HTML 파일
텍스트/css|CSS(스타일시트)
application/x-javascript|JavaScript
application/javascript|JavaScript

주요 정보:

- 각각 하나의 공백으로 구분하여 여러 인터넷 미디어 유형을 지정합니다. 
- 이 기능은 1MB 미만 크기의 자산만 압축합니다. 더 큰 자산은 서버에서 압축하지 않습니다.
- 이미지, 비디오 및 오디오 미디어 자산(예: JPG, MP3, MP4 등)과 같은 특정 유형의 콘텐츠는 이미 압축되어 있습니다. 이러한 유형의 자산은 추가로 압축해도 파일 크기가 크게 줄어들지 않기 때문에 압축을 사용하지 않는 것이 좋습니다.
- 별표와 같은 와일드카드 문자는 지원되지 않습니다.
- 규칙에이 기능을 추가 하기 전에이 규칙이 적용 되는 플랫폼의 압축 페이지에서 압축 사용 안 함 옵션을 설정 해야 합니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>사용자 지정 로그 필드 1
**목적:** 원시 로그 파일의 사용자 지정 로그 필드에 할당할 콘텐츠와 형식을 결정 합니다.

이 사용자 지정 필드를 사용하면 로그 파일에 저장할 요청 및 응답 헤더 값을 결정할 수 있습니다.

기본적으로 사용자 지정 로그 필드는 "x-ec_custom-1"입니다. 이 필드의 이름은 원시 로그 설정 페이지에서 사용자 지정할 수 있습니다.

요청 헤더와 응답 헤더를 지정하는 형식은 다음과 같이 정의됩니다.

헤더 형식|형식|예
-|-|-
요청 헤더|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
응답 헤더|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

주요 정보:

- 사용자 지정 로그 필드는 헤더 필드와 일반 텍스트의 조합을 포함할 수 있습니다.
- 이 필드에 사용할 수 있는 문자는 영숫자(0-9, a-z 및 A-Z), 대시, 콜론, 세미콜론, 아포스트로피, 쉼표, 마침표, 밑줄, 등호, 괄호, 대괄호 및 공백입니다. 백분율 기호와 중괄호는 헤더 필드를 지정할 때만 사용할 수 있습니다.
- 지정된 각 헤더 필드의 철자는 원하는 요청/응답 헤더 이름과 일치해야 합니다.
- 헤더를 여러 개 지정하려면 구분 기호를 사용하여 각 헤더를 나타냅니다. 예를 들어 각 헤더에 약어를 사용할 수 있습니다.
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**기본값:** -

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>디버그 캐시 응답 헤더
**목적:** 응답에 포함할 수 있는지 여부를 결정 [X-EC-디버그 응답 헤더](cdn-http-debug-headers.md), 요청된 된 자산에 대 한 캐시 정책 정보를 제공 하는 합니다.

디버그 캐시 응답 헤더는 다음 두 가지 모두에 해당하는 경우 응답에 포함됩니다.

- 디버그 캐시 응답 헤더 기능은 지정된 요청에서 활성화됩니다.
- 지정된 요청은 응답에 포함될 디버그 캐시 응답 헤더 집합을 정의합니다.

디버그 캐시 응답 헤더는 요청에 다음 헤더 및 지정된 지시문을 포함하여 요청할 수 있습니다.

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**예제:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

값|결과
-|-
사용하도록 설정됨|디버그 캐시 응답 헤더에 대 한 요청 X-EC-디버그 헤더를 포함 하는 응답을 반환 합니다.
비활성화됨|X-EC-디버그 응답 헤더 응답에서 제외 됩니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>기본 내부 Max-Age
**목적:** pop에서 원본 서버 캐시 유효성 재검사 기본 max-age 간격을 결정 합니다. 즉, POP에서 캐시된 자산이 원본 서버에 저장된 자산과 일치하는지 여부를 확인할 때까지 경과할 시간입니다.

주요 정보:

- 이 작업 진행만 됩니다 응답에 max-age 표시를 할당 하지 않은 원본 서버의 합니다 `Cache-Control` 또는 `Expires` 헤더입니다.
- 이 작업은 캐시 가능하다고 간주되지 않는 자산에 대해서는 수행되지 않습니다.
- 이 작업은 브라우저-POP 캐시 유효성 재검사에 영향을 주지 않습니다. 이러한 유형의 유효성 재검사에 의해 결정 되는 `Cache-Control` 또는 `Expires` 외부 Max-age 기능으로 사용자 지정할 수 있는 브라우저로 전송 되는 헤더입니다.
- 이 작업의 결과는 응답 헤더와 콘텐츠의 POP에서 반환된 콘텐츠에 대해 관찰 가능한 영향을 주지 않지만, POP에서 원본 서버로 보내는 유효성 검사 트래픽의 양에는 영향을 줄 수 있습니다.
- 이 기능은 다음과 같이 구성합니다.
    - 기본 내부 max-age 간격을 적용할 수 있는 상태 코드를 선택합니다.
    - 정수 값을 지정한 다음 원하는 시간 단위(예: 초, 분, 시간 등)를 선택합니다. 이 값은 기본 내부 max-age 간격을 정의합니다.

- 할당에 max-age 표시를 할당 되지 않은 요청에 대 한 일의 기본 내부 max-age 간격 시간 단위를 "끄기"로 설정 자신의 `Cache-Control` 하거나 `Expires` 헤더입니다.

**기본값:** 7 일

#### <a name="compatibility"></a>호환성
캐시 설정을 추적하는 방식으로 인해 이 기능은 다음 일치 조건과 연결할 수 없습니다. 
- AS 숫자
- 클라이언트 IP 주소
- 쿠키 매개 변수
- 쿠키 매개 변수 Regex
- 국가
- 장치
- 에지 Cname
- 참조 도메인
- 요청 헤더 리터럴
- 요청 헤더 Regex
- 요청 헤더 와일드카드
- 요청 메서드
- 요청 스키마
- URL 쿼리 리터럴
- URL 쿼리 Regex
- URL 쿼리 와일드카드
- URL 쿼리 매개 변수

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>액세스 거부(403)
**목적은**: 모든 요청이 403 사용 권한 없음 응답으로 거부되는지 여부를 결정합니다.

값 | 결과
------|-------
사용하도록 설정됨| 403 사용할 수 없음 응답으로 거부되는 일치 조건을 충족하는 요청을 모두 거부하도록 합니다.
비활성화됨| 기본 동작을 복원합니다. 기본 동작은 원본 서버에서 반환될 응답 형식을 결정할 수 있도록 허용하는 것입니다.

**기본 동작**: 비활성화됨

> [!TIP]
   > 이 기능의 가능한 용도 한 가지는 요청 헤더 일치 조건과 연결하여 콘텐츠에 대한 인라인 링크를 사용하는 HTTP 참조 페이지에 대한 액세스를 차단하는 것입니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>만료 헤더 처리
**목적:** 생성을 제어 합니다 `Expires` 외부 Max-age 기능이 활성 상태일 때 POP 헤더입니다.

이 유형의 구성을 획득하는 가장 쉬운 방법은 동일한 문에 외부 Max-Age와 Expires 헤더 처리 기능을 배치하는 것입니다.

값|결과
--|--
덮어쓰기|다음과 같은 작업이 수행되도록 합니다.<br/>-기존의 합니다 `Expires` 원본 서버에서 생성 된 헤더입니다.<br/>-추가 합니다 `Expires` 헤더 응답에 외부 Max-age 기능으로 생성 합니다.
통과|있는지 확인 합니다 `Expires` 외부 Max-age 기능으로 생성 한 헤더를 응답에 절대 추가 합니다. <br/> 원본 서버에서 생성 되는 경우는 `Expires` 헤더를 전달 하면 최종 사용자에 게 합니다. <br/>원본 서버를 생성 하지 않는 경우는 `Expires` 헤더를 다음이 옵션은 응답 헤더에 포함 되어 있지 않을 `Expires` 헤더입니다.
없는 경우 추가| 경우는 `Expires` 헤더는 원본 서버에서 수신 하지 않은 다음이 옵션을 추가 합니다 `Expires` 외부 Max-age 기능으로 생성 된 헤더입니다. 이 옵션은 모든 자산 할당 하도록 하는 데 유용 프로그램 `Expires` 헤더입니다.
제거| 되도록를 `Expires` 헤더 헤더 응답에 포함 되지 않습니다. 경우는 `Expires` 헤더가 이미 할당 된 다음 헤더 응답에서 제거 됩니다.

**기본 동작:** 덮어쓰기

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>외부 Max-Age
**목적:** 브라우저에서 POP 캐시 유효성 재검사를 위한 max-age 간격을 결정 합니다. 즉, 브라우저에서 POP에 있는 새 버전의 자산을 확인할 수 있을 때까지 경과할 시간입니다.

이 기능을 사용 하면 생성 됩니다 `Cache-Control: max-age` 및 `Expires` 헤더에서 Pop 및 HTTP 클라이언트로 전송 합니다. 기본적으로 이러한 헤더는 원본 서버에서 만든 헤더를 덮어씁니다. 그러나이 동작을 변경 하는 Cache-control 헤더 처리 및 Expires 헤더 처리 기능을 사용할 수 있습니다.

주요 정보:

- 이 작업은 POP-원본 서버 캐시 유효성 재검사에 영향을 주지 않습니다. 이러한 유형의 유효성 재검사에 의해 결정 됩니다 합니다 `Cache-Control` 하 고 `Expires`  원본 서버에서 받은 머리글과 기본 내부 Max-age 및 강제 내부 Max-age 기능을 사용 하 여 사용자 지정할 수 있습니다.
- 정수 값을 지정한 다음 원하는 시간 단위(예: 초, 분, 시간 등)를 선택하여 이 기능을 구성합니다.
- 이 기능을 음수 값으로 설정 하면 송신 Pop를 `Cache-Control: no-cache` 와 `Expires` 브라우저에 대 한 각 응답과 함께 과거에 설정 된 시간입니다. HTTP 클라이언트에서 응답을 캐시하지 않지만 이 설정은 원본 서버에서 응답을 캐시하는 POP의 기능에 영향을 주지 않습니다.
- 시간 단위를 "끄기"로 설정하면 이 기능을 비활성화합니다. 합니다 `Cache-Control` 하 고 `Expires`  원본 서버의 응답을 사용 하 여 캐시 헤더는 브라우저로 전달 됩니다.

**기본 동작:** 해제

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>리디렉션 추적
**목적:** 고객 원본 서버에서 반환한 Location 헤더에 정의 된 호스트 이름으로 요청을 리디렉션할 수 있는지 여부를 결정 합니다.

주요 정보:

- 요청은 동일한 플랫폼에 해당하는 에지 CNAME으로만 리디렉션할 수 있습니다.

값|결과
-|-
사용하도록 설정됨|요청을 리디렉션할 수 있습니다.
비활성화됨|요청을 리디렉션하지 않습니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>강제 내부 Max-Age
**목적:** pop에서 원본 서버 캐시 유효성 재검사 max-age 간격을 결정 합니다. 즉, POP에서 캐시된 자산이 원본 서버에 저장된 자산과 일치하는지 여부를 확인할 때까지 경과할 시간입니다.

주요 정보:

- 이 기능에 정의 된 max-age 간격을 덮어씁니다 `Cache-Control` 하거나 `Expires` 원본 서버에서 생성 된 헤더입니다.
- 이 기능은 브라우저-POP 캐시 유효성 재검사에 영향을 주지 않습니다. 이러한 유형의 유효성 재검사에 의해 결정 되는 `Cache-Control` 또는 `Expires` 브라우저로 전송 되는 헤더입니다.
- 이 기능은 POP에서 요청자에게 전달한 응답에 관찰 가능한 영향을 주지 않습니다. 그러나 POP에서 원본 서버로 보낸 유효성 재검사 트래픽의 양에 영향을 줄 수 있습니다.
- 이 기능은 다음과 같이 구성합니다.
    - 내부 max-age 간격을 적용할 상태 코드를 선택합니다.
    - 정수 값을 지정하고 원하는 시간 단위(예: 초, 분, 시간 등)를 선택합니다. 이 값은 요청의 max-age 간격을 정의합니다.

- 시간 단위를 "끄기"로 설정하면 이 기능을 비활성화합니다. 내부 max-age 간격은 요청된 자산에 할당되지 않습니다. 원래 헤더에 캐싱 지침이 없으면 자산 기본 내부 Max-age 기능의 활성 설정에 따라 캐시 됩니다.

**기본 동작:** 해제

#### <a name="compatibility"></a>호환성
캐시 설정을 추적하는 방식으로 인해 이 기능은 다음 일치 조건과 연결할 수 없습니다. 
- AS 숫자
- 클라이언트 IP 주소
- 쿠키 매개 변수
- 쿠키 매개 변수 Regex
- 국가
- 장치
- 에지 Cname
- 참조 도메인
- 요청 헤더 리터럴
- 요청 헤더 Regex
- 요청 헤더 와일드카드
- 요청 메서드
- 요청 스키마
- URL 쿼리 리터럴
- URL 쿼리 Regex
- URL 쿼리 와일드카드
- URL 쿼리 매개 변수

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264 지원(HTTP 점진적 다운로드)
**목적:** 콘텐츠 스트리밍에 사용할 수 있는 H.264 파일 형식의 유형을 결정 합니다.

주요 정보:

- 파일 확장명 옵션에서 허용 되는 H.264 파일 이름 확장명의 공백으로 구분 된 집합을 정의 합니다. 파일 확장명 옵션을 기본 동작을 재정의 합니다. 이 옵션을 설정할 때 파일 이름 확장명을 포함하여 MP4 및 F4V 지원을 유지합니다. 
- 각 파일 이름 확장명을 지정하는 경우 마침표를 포함합니다(예: _.mp4_, _.f4v_).

**기본 동작:** 기본적으로 MP4 및 F4V 미디어를 지 원하는 HTTP 점진적 다운로드 합니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>No-Cache 요청 부여
**목적:** 원본 서버에 요청을 전달할지 HTTP 클라이언트의 no-cache 여부를 결정 합니다.

No-cache 요청 HTTP 클라이언트에 보낼 때 발생 한 `Cache-Control: no-cache` 및/또는 `Pragma: no-cache` HTTP 요청의 헤더입니다.

값|결과
--|--
사용하도록 설정됨|HTTP 클라이언트의 no-cache 요청을 원본 서버로 전달할 수 있으며, 원본 서버에서 POP를 통해 응답 헤더와 본문을 HTTP 클라이언트에 다시 반환합니다.
비활성화됨|기본 동작을 복원합니다. 기본 동작은 no-cache 요청을 원본 서버로 전달하지 않도록 하는 것입니다.

모든 프로덕션 트래픽에 대해 이 기능을 기본 비활성 상태로 유지하는 것이 좋습니다. 그렇지 않으면 실수로 웹 페이지를 새로 고칠 때 많은 no-cache 요청을 트리거할 수 있는 최종 사용자 또는 모든 비디오 요청과 함께 no-cache 헤더를 보내도록 코딩된 인기 있는 다양한 미디어 플레이어로부터 원본 서버를 보호하지 않습니다. 그럼에도 불구하고 이 기능은 원본 서버에서 요청 시 새로운 콘텐츠를 끌어올 수 있도록 특정 비프로덕션 스테이징 또는 테스트 디렉터리에 적용하는 것이 유용할 수 있습니다.

이 기능에 따라 원본 서버로 전달 될 수 있는 요청에 대 한 보고 되는 캐시 상태 `TCP_Client_Refresh_Miss`합니다. 코어 보고 모듈에서에서 사용할 수 있는 캐시 상태 보고서는 캐시 상태별 통계 정보를 제공 합니다. 이 보고서를 사용하면 이 기능에 따라 원본 서버로 전달되는 요청의 수와 백분율을 추적할 수 있습니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>원본 No-Cache 무시
**목적:** CDN에서 원본 서버에서 제공 되는 다음 지시문을 무시할지 여부를 결정 합니다.

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

주요 정보:

- 위의 지시문을 무시할 상태 코드의 공백으로 구분된 목록을 정의하여 이 기능을 구성합니다.
- 이 기능에 대 한 유효한 상태 코드 집합은 다음과 같습니다. 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 및 505입니다.
- 이 기능을 빈 값으로 설정하면 비활성화합니다.

**기본 동작:** 기본 동작은 위의 지시문을 적용 하는 것입니다.

#### <a name="compatibility"></a>호환성
캐시 설정을 추적하는 방식으로 인해 이 기능은 다음 일치 조건과 연결할 수 없습니다. 
- AS 숫자
- 클라이언트 IP 주소
- 쿠키 매개 변수
- 쿠키 매개 변수 Regex
- 국가
- 장치
- 에지 Cname
- 참조 도메인
- 요청 헤더 리터럴
- 요청 헤더 Regex
- 요청 헤더 와일드카드
- 요청 메서드
- 요청 스키마
- URL 쿼리 리터럴
- URL 쿼리 Regex
- URL 쿼리 와일드카드
- URL 쿼리 매개 변수

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>적절하지 않은 범위 무시 
**목적:** 요청에서 416 요청한 범위가 충분 하지 않음 상태 코드를 생성 하는 경우 클라이언트로 반환할 응답을 결정 합니다.

기본적으로이 상태 코드는 POP에서 지정 된 바이트 범위 요청을 충족할 수 없고 If-range 요청 헤더 필드를 지정 하지 않았습니다 때 반환 됩니다.

값|결과
-|-
사용하도록 설정됨|Pop에서를 416 요청한 범위가 충분 하지 않음 상태 코드를 사용 하 여 잘못 된 바이트 범위 요청에 응답할 수 없습니다. 대신 서버 요청된 된 자산을 제공 하 고 클라이언트에 200 OK 반환 됩니다.
비활성화됨|기본 동작을 복원합니다. 기본 동작은 416 요청한 범위가 충분 하지 않음 상태 코드를 적용 하도록 합니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>내부 Max-Stale
**목적:** 캐시 된 자산을 POP에 캐시 된 자산이 원본 서버를 사용 하 여 유효성을 다시 검사할 수 없는 경우 POP에서 제공 될 수 있는 정상 만료 시간 지 나 얼마나 오래 컨트롤입니다.

일반적으로 자산의 max-age 간격이 만료 되 면 POP는 유효성 재검사 요청을 원본 서버로 보냅니다. 원본 서버에서 다음 응답 304 수정 되지 POP에 새로 빌려 캐시 된 자산에 200으로 확인을 POP에 캐시 된 자산의 업데이트 된 버전을 사용 하 여 제공 합니다.

이러한 유효성 재검사를 시도하는 동안 POP에서 원본 서버와의 연결을 설정할 수 없는 경우 내부 Max-Stale 기능은 POP에서 현재의 부실 자산을 계속 제공할지 여부와 기간을 제어합니다.

이 시간 간격이 자산의 최대 처리 기간 만료 되 면 되지 경우 실패 한 유효성 재검사를 시작 하는 note 합니다. 따라서 최대 하는 동안 자산 제공 될 수 있지만 성공적인 유효성 재검사 없이 기간은 최대 처리 기간와 max-stale의 조합에 의해 지정 된 시간입니다. 예를 들어, 자산을 9 시 30 분의 max-age 및 15 분의 max-stale에 캐시 된 경우 다음 9 시 44 실패 한 유효성 재검사 시도 초래 하는 반면 9 시 46는 실패 한 유효성 재검사 시도 en 캐시 된 부실 자산을 수신 하는 최종 사용자 504 게이트웨이 시간 초과가 수신 d 사용자입니다.

이 기능에 의해 대체에 대해 구성 된 모든 값 `Cache-Control: must-revalidate` 또는 `Cache-Control: proxy-revalidate` 헤더를 원본 서버에서 수신 합니다. 자산을 처음 캐시할 때 원본 서버로부터 이러한 헤더 중 하나를 수신하면 POP에서 캐시된 부실 자산을 제공하지 않습니다. 이러한 경우 POP 자산의 max-age 간격이 만료 되 면 원본 유효성 검사를 다시 수 없는 경우 POP 504 게이트웨이 시간 초과 오류를 반환 합니다.

주요 정보:

- 이 기능은 다음과 같이 구성합니다.
    - max-stale 간격을 적용할 상태 코드를 선택합니다.
    - 정수 값을 지정한 다음 원하는 시간 단위(예: 초, 분, 시간 등)를 선택합니다. 이 값은 내부 max-stale 적용 되는 정의 합니다.

- 시간 단위를 "끄기"로 설정하면 이 기능을 비활성화합니다. 캐시된 자산은 정상 만료 시간을 초과하여 처리되지 않습니다.

**기본 동작:** 2 분

#### <a name="compatibility"></a>호환성
캐시 설정을 추적하는 방식으로 인해 이 기능은 다음 일치 조건과 연결할 수 없습니다. 
- AS 숫자
- 클라이언트 IP 주소
- 쿠키 매개 변수
- 쿠키 매개 변수 Regex
- 국가
- 장치
- 에지 Cname
- 참조 도메인
- 요청 헤더 리터럴
- 요청 헤더 Regex
- 요청 헤더 와일드카드
- 요청 메서드
- 요청 스키마
- URL 쿼리 리터럴
- URL 쿼리 Regex
- URL 쿼리 와일드카드
- URL 쿼리 매개 변수

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>로그 쿼리 문자열
**목적:** 쿼리 문자열로 액세스 로그에 URL과 함께 저장할지 여부를 결정 합니다.

값|결과
-|-
사용하도록 설정됨|액세스 로그에 URL을 기록할 때 쿼리 문자열을 저장할 수 있습니다. URL에 쿼리 문자열이 없으면 이 옵션이 적용되지 않습니다.
비활성화됨|기본 동작을 복원합니다. 기본 동작은 액세스 로그에 URL을 기록할 때 쿼리 문자열을 무시하는 것입니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>최대 연결 유지 요청
**목적:** 연결이 닫히기 전에 연결 유지 연결에 대 한 요청의 최대 수를 정의 합니다.

최대 요청 수를 낮은 값으로 설정하는 것은 권장되지 않으며 성능이 저하될 수 있습니다.

주요 정보:

- 이 값은 0을 포함한 양의 정수로 지정합니다.
- 지정된 값에 쉼표 또는 마침표를 포함하면 안됩니다.

**기본값:** 10,000 개 요청

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>클라이언트 요청 헤더 수정
**목적:** 각 요청에 설명 하는 요청 헤더 집합이 포함 되어 있습니다. 이 기능은 다음 중 하나를 수행할 수 있습니다.

- 요청 헤더에 할당된 값을 추가하거나 덮어씁니다. 지정된 요청 헤더가 없는 경우 이 기능은 요청에 해당 헤더를 추가합니다.
- 요청에서 요청 헤더를 삭제합니다.

원본 서버로 전달되는 요청에는 이 기능에서 변경한 내용이 반영됩니다.

요청 헤더에서 다음 작업 중 하나를 수행할 수 있습니다.

옵션|설명|예
-|-|-
추가|지정된 값이 기존 요청 헤더 값의 끝에 추가됩니다.|**요청 헤더 값(클라이언트):**<br/>값1<br/>**요청 헤더 값(규칙 엔진):**<br/>값2 <br/>**새 요청 헤더 값:** <br/>Value1Value2
덮어쓰기|요청 헤더 값을 지정된 된 값으로 설정합니다.|**요청 헤더 값(클라이언트):**<br/>값1<br/>**요청 헤더 값(규칙 엔진):**<br/>값2<br/>**새 요청 헤더 값:**<br/> 값2 <br/>
삭제|지정된 요청 헤더를 삭제합니다.|**요청 헤더 값(클라이언트):**<br/>값1<br/>**클라이언트 요청 헤더 구성 수정:**<br/>요청 헤더를 질문에서 삭제합니다.<br/>**결과:**<br/>지정된 요청 헤더를 원본 서버로 전달하지 않습니다.

주요 정보:

- 이름 옵션에 지정 된 값이 원하는 요청 헤더와 정확히 일치 인지 확인 합니다.
- 헤더를 식별하기 위해 사례를 사용하지 않습니다. 변화를 다음 중 하나에 예를 들어 합니다 `Cache-Control` 헤더 이름을 식별 하기 위해 사용할 수 있습니다.
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 헤더 이름을 지정할 때는 영숫자 문자, 대시 또는 밑줄만 사용합니다.
- 헤더를 삭제하면 POP에서 해당 헤더를 원본 서버로 전달하지 않습니다.
- 다음 헤더는 예약되어 있으며, 이 기능으로 수정할 수 없습니다.
    - forwarded
    - 호스트
    - via
    - Warning
    - x-forwarded-for
    - "x-ec"로 시작하는 모든 헤더 이름은 예약되어 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>클라이언트 응답 헤더 수정
각 응답에는 설명 하는 응답 헤더 집합이 포함 되어 있습니다. 이 기능은 다음 중 하나를 수행할 수 있습니다.

- 응답 헤더에 할당된 값을 추가하거나 덮어씁니다. 지정된 응답 헤더가 없는 경우 이 기능은 응답에 해당 헤더를 추가합니다.
- 응답에서 응답 헤더를 삭제합니다.

기본적으로 응답 헤더 값은 원본 서버 및 POP에서 정의합니다.

응답 헤더에서 다음 작업 중 하나를 수행할 수 있습니다.

옵션|설명|예
-|-|-
추가|지정된 값이 기존 응답 헤더 값의 끝에 추가됩니다.|**응답 헤더 값(클라이언트):**<br />값1<br/>**응답 헤더 값(규칙 엔진):**<br/>값2<br/>**새 응답 헤더 값:**<br/>Value1Value2
덮어쓰기|응답 헤더 값을 지정된 값으로 설정합니다.|**응답 헤더 값(클라이언트):**<br/>값1<br/>**응답 헤더 값(규칙 엔진):**<br/>값2 <br/>**새 응답 헤더 값:**<br/>값2 <br/>
삭제|지정된 응답 헤더를 삭제합니다.|**응답 헤더 값(클라이언트):**<br/>값1<br/>**클라이언트 응답 헤더 구성 수정:**<br/>응답 헤더를 질문에서 삭제합니다.<br/>**결과:**<br/>지정된 응답 헤더를 요청자에게 전달하지 않습니다.

주요 정보:

- 이름 옵션에 지정 된 값이 원하는 응답 헤더와 정확히 일치 인지 확인 합니다. 
- 헤더를 식별하기 위해 사례를 사용하지 않습니다. 변화를 다음 중 하나에 예를 들어 합니다 `Cache-Control` 헤더 이름을 식별 하기 위해 사용할 수 있습니다.
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 헤더를 삭제하면 요청자에게 해당 헤더를 전달하지 않습니다.
- 다음 헤더는 예약되어 있으며, 이 기능으로 수정할 수 없습니다.
    - accept-encoding
    - age
    - 연결
    - content-encoding
    - content-length
    - content-range
    - 날짜
    - 서버
    - trailer
    - transfer-encoding
    - 업그레이드
    - vary
    - via
    - 경고
    - "x-ec"로 시작하는 모든 헤더 이름은 예약되어 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>부분 캐시 공유
**목적:** 요청에서 부분적으로 캐시 된 콘텐츠를 생성할 수 있는지 여부를 결정 합니다.

이 부분 캐시는 요청된 콘텐츠를 완전히 캐시할 때까지 해당 콘텐츠에 대한 새 요청을 수행하는 데 사용할 수 있습니다.

값|결과
-|-
사용하도록 설정됨|요청에서 부분적으로 캐시된 콘텐츠를 생성할 수 있습니다.
비활성화됨|요청에서 요청한 콘텐츠에 대해 완전하게 캐시된 버전만 생성할 수 있습니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>캐시된 콘텐츠 사전 유효성 검사
**목적:** 여부를 결정 캐시 된 콘텐츠가 초기 유효성 재검사에 적합 한 TTL이 만료 되기 전에 합니다.

초기 유효성 재검사에 적합한 동안 요청된 콘텐츠의 TTL이 만료되기까지의 시간을 정의합니다.

주요 정보:

- 시간 단위를 "끄기"로 선택하여 캐시된 콘텐츠의 TTL이 만료된 후에 유효성 재검사를 수행해야 합니다. 시간은 지정하지 않아야 하며, 무시됩니다.

**기본 동작:** 해제 합니다. 유효성 재검사는 캐시된 콘텐츠의 TTL이 만료된 후에만 수행할 수 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>프록시 특별 헤더
**목적:** 집합을 정의 합니다 [Verizon 특정 HTTP 요청 헤더](cdn-verizon-http-headers.md) 는 전달할 POP에서 원본 서버입니다.

주요 정보:

- 이 기능에 정의된 각 CDN 특정 요청 헤더는 원본 서버로 전달됩니다. 제외된 헤더는 전달되지 않습니다.
- CDN 특정 요청 헤더가 전달되지 않도록 하려면 헤더 목록 필드의 공백으로 구분된 목록에서 제거합니다.

기본 목록에 포함되는 HTTP 헤더는 다음과 같습니다.
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- 호스트

**기본 동작:** 모든 CDN 특정 요청 헤더를 원본 서버로 전달 됩니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>0바이트 캐시 파일 새로 고침
**목적:** Pop에 의해 0 바이트 캐시 자산에 대 한 HTTP 클라이언트의 요청을 처리 하는 방법을 결정 합니다.

유효한 값은

값|결과
--|--
사용하도록 설정됨|POP가 원본 서버로부터 자산을 다시 가져오도록 합니다.
비활성화됨|기본 동작을 복원합니다. 기본 동작은 요청 시 유효한 캐시 자산을 제공하는 것입니다.

이 기능은 올바른 캐싱 및 콘텐츠 배달에는 필요하지 않지만 해결 방법으로는 유용할 수 있습니다. 예를 들어 원본 서버의 동적 콘텐츠 생성기로 인해 실수로 0바이트 응답을 POP로 보낼 수 있습니다. 이러한 유형의 응답은 일반적으로 POP에서 캐시합니다. 0바이트 응답이 이러한 콘텐츠에 대해 유효한 응답이 아님을 알고 있는 경우 

이 기능을 통해 이러한 유형의 자산을 클라이언트에 제공하지 못하도록 방지할 수 있습니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>캐시 가능한 상태 코드 집합
**목적:** 캐시 된 콘텐츠가 발생할 수 있는 상태 코드 집합을 정의 합니다.

기본적으로 캐싱을 사용할 수 200 OK 응답 합니다.

원하는 상태 코드의 공백으로 구분된 집합을 정의합니다.

주요 정보:

- 원본 No-cache 무시 기능을 사용 하도록 설정 합니다. 이 기능을 사용 하지 않는 경우 비 200 확인 응답이 수 캐시 되지 않습니다.
- 이 기능에 대 한 유효한 상태 코드 집합은 다음과 같습니다. 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 및 505입니다.
- 200 OK 상태 코드를 생성 하는 응답에 대 한 캐싱을 사용 하지 않으려면이 기능을 사용할 수 없습니다.

**기본 동작:** 캐싱은 200 확인 상태 코드를 생성 하는 응답에 대해서만 사용할 수 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>클라이언트 IP 사용자 지정 헤더 설정
**목적:** 요청에 대 한 IP 주소별로 요청 클라이언트를 식별 하는 사용자 지정 헤더를 추가 합니다.

헤더 이름 옵션에는 클라이언트의 IP 주소가 저장 되어 있는 사용자 지정 요청 헤더의 이름을 정의 합니다.

이 기능을 사용하면 고객 원본 서버에서 사용자 지정 요청 헤더를 통해 클라이언트 IP 주소를 확인할 수 있습니다. 캐시에서 요청을 제공하면 클라이언트의 IP 주소를 원본 서버에 알리지 않습니다. 따라서 캐시 되지 않은 자산을 사용 하 여이 기능을 사용 하는 것이 좋습니다.

지정된 헤더 이름이 다음 중 하나와 일치하는지 확인합니다.

- 표준 요청 헤더 이름 - 표준 헤더 이름 목록에서 찾을 수 있습니다 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)합니다.
- 예약된 헤더 이름
    - forwarded-for
    - 호스트
    - vary
    - via
    - Warning
    - x-forwarded-for
    - "x-ec"로 시작하는 모든 헤더 이름은 예약되어 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>오류 시 오래된 콘텐츠 배달
**목적:** 고객 원본 서버에서 요청 된 콘텐츠를 검색할 때 캐시 유효성 재검사 중 이나 오류가 발생 하는 경우 만료 된 캐시 된 콘텐츠를 배달할지 여부를 결정 합니다.

값|결과
-|-
사용하도록 설정됨|원본 서버에 연결하는 동안 오류가 발생하면 요청자에게 부실 콘텐츠를 제공합니다.
비활성화됨|요청자에게 원본 서버의 오류를 전달합니다.

**기본 동작:** 사용 안 함

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>유효성 재검사 중 기한 경과
**목적:** pop 유효성 재검사를 수행 하는 동안 요청자에 게 부실 콘텐츠를 제공 하도록 하 여 성능을 개선 합니다.

주요 정보:

- 이 기능의 동작은 선택한 시간 단위에 따라 다릅니다.
    - **시간 단위:** 시간의 길이 지정 하 고 부실 콘텐츠 배달을 허용 하려면 시간 단위 (예를 들어, 초, 분, 시간 등)를 선택 합니다. 이 유형의 설정 사용 하면 수를 제공 하는 시간의 길이 확장 하는 CDN 콘텐츠를 다음 수식에 따라 유효성 검사를 요구 하기 전에: **TTL** + **재검사 시간 동안 오래 된** 
    - **Off:** 선택 하려면 "끄기" 부실 콘텐츠 제공 될 수 있는지에 대 한 요청을 하기 전에 유효성 재검사를 요구 합니다.
        - 기간은 적용할 수 없으므로 지정하지 않아야 하며, 무시됩니다.

**기본 동작:** 해제 합니다. 유효성 재검사는 요청된 콘텐츠를 제공할 수 있기 전에 수행해야 합니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>토큰 인증
**목적:** 토큰 기반 인증 요청에 적용될지 여부를 결정 합니다.

토큰 기반 인증을 사용하는 경우 암호화된 토큰을 제공하고 해당 토큰에서 지정한 요구 사항을 준수하는 요청만 허용합니다.

암호화 및 토큰 값을 암호 해독에 사용 되는 암호화 키의 기본 키 및 토큰 인증 페이지에서 백업 키 옵션으로 결정 됩니다. 암호화 키는 플랫폼에 따라 다릅니다.

**기본 동작:** 사용 안 함.

이 기능은 URL 다시 쓰기 기능을 제외하고 대부분의 기능보다 우선합니다.

값 | 결과
------|---------
사용하도록 설정됨 | 토큰 기반 인증으로 요청된 콘텐츠를 보호합니다. 유효한 토큰을 제공하고 요구 사항을 충족하는 클라이언트의 요청만 허용합니다. FTP 트랜잭션은 토큰 기반 인증에서 제외됩니다.
비활성화됨| 기본 동작을 복원합니다. 기본 동작은 토큰 기반 인증 구성으로 요청을 보안할 수 있도록 허용하는 것입니다.

#### <a name="compatibility"></a>호환성
항상 일치 조건이 있는 토큰 인증은 사용하지 않습니다. 

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>토큰 인증 거부 코드
**목적:** 토큰 기반 인증 요청을 거부할 때 사용자에 게 반환할 응답 유형을 결정 합니다.

사용 가능한 응답 코드는 다음 표에 나열됩니다.

응답 코드|응답 이름|설명
-------------|-------------|--------
301|영구적으로 이동됨|이 상태 코드는 Location 헤더에 지정 된 URL에 권한이 없는 사용자를 리디렉션합니다.
302|찾음|이 상태 코드는 Location 헤더에 지정 된 URL에 권한이 없는 사용자를 리디렉션합니다. 리디렉션을 수행하는 업계 표준 방식입니다.
307|임시 리디렉션|이 상태 코드는 Location 헤더에 지정 된 URL에 권한이 없는 사용자를 리디렉션합니다.
401|인증되지 않음|이 상태 코드를 Www-authenticate 응답 헤더를 사용 하 여 결합 인증에 대 한 사용자에 게 수 있습니다.
403|사용할 수 없음|이 메시지는 보호 된 콘텐츠에 액세스 하려고 할 때 권한 없는 사용자에 게 표시할 표준 403 사용할 수 없음 상태 메시지입니다.
404|파일을 찾을 수 없음|HTTP 클라이언트에서 서버와 통신할 수 있지만 요청한 콘텐츠를 찾을 수 없음을 나타냅니다.

#### <a name="compatibility"></a>호환성
항상 일치 조건이 있는 토큰 인증 거부 코드는 사용하지 않습니다. 대신 **관리** 포털의 **토큰 인증** 페이지에 있는 **사용자 지정 거부 처리** 섹션을 사용합니다. 자세한 내용은 [토큰 인증을 사용하여 Azure CDN 자산 보안 유지](cdn-token-auth.md)를 참조하세요.

#### <a name="url-redirection"></a>URL 리디렉션

이 기능은 3xx 상태 코드를 반환 하도록 구성 된 경우 사용자 정의 URL에 대 한 URL 리디렉션을 지원 합니다. 이 사용자 정의 URL은 다음 단계를 수행하여 지정할 수 있습니다.

1. 토큰 인증 거부 코드 기능에 대 한 3xx 응답 코드를 선택 합니다.
2. 선택적 헤더 이름 옵션에서 "위치"를 선택 합니다.
3. 선택적 헤더 값 옵션을 원하는 URL로 설정 합니다.

URL 3xx 상태 코드를 정의 하지 않은 경우 3xx 상태 코드에 대 한 표준 응답 페이지는 사용자에 게 반환 됩니다.

만 URL 리디렉션은 3xx 응답 코드에 적용 됩니다.

선택적 헤더 값 옵션에는 영숫자, 인용 부호 및 공백을 지원합니다.

#### <a name="authentication"></a>인증

이 기능은 토큰 기반 인증으로 보호 된 콘텐츠에 대 한 권한이 없는 요청에 응답할 때 Www-authenticate 헤더를 포함 하는 기능을 지원 합니다. 구성에서 Www-authenticate 헤더가 "basic"으로 설정한, 경우 권한이 없는 사용자 계정 자격 증명에 대 한 메시지가 있습니다.

위의 구성은 다음 단계를 수행하여 획득할 수 있습니다.

1. 토큰 인증 거부 코드 기능의 응답 코드로 "401"을 선택합니다.
2. 선택적 헤더 이름 옵션에서 "Www-authenticate"를 선택 합니다.
3. 선택적 헤더 값 옵션을 "basic."를 설정 합니다.

Www-authenticate 헤더만 401 응답 코드에 적용 됩니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>토큰 인증 URL 대/소문자 무시
**목적:** 토큰 기반 인증에서 URL 비교 시 대/소문자 구분 여부를 결정 합니다.

이 기능으로 영향을 받는 매개 변수는 다음과 같습니다.

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

유효한 값은

값|결과
---|----
사용하도록 설정됨|토큰 기반 인증 매개 변수에 대한 URL을 비교할 때 POP에서 대/소문자를 무시하도록 합니다.
비활성화됨|기본 동작을 복원합니다. 기본 동작은 토큰 인증을 위한 URL 비교에서 대/소문자를 구분하는 것입니다.

**기본 동작:** 사용 안 함.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>토큰 인증 매개 변수
**목적:** 토큰 기반 인증 쿼리 문자열 매개 변수 이름을 바꿔야 하는지 여부를 결정 합니다.

주요 정보:

- 값 옵션 이름을 정의 합니다 쿼리 문자열 매개 변수는 토큰을 지정할 수 있습니다.
- "Ec_token." 값 옵션을 설정할 수 없습니다.
- 값 옵션에 정의 된 이름에 유효한 URL 문자만 포함 되어 있는지를 확인 합니다.

값|결과
----|----
사용하도록 설정됨|값 옵션에는 토큰을 정의 해야 쿼리 문자열 매개 변수 이름을 정의 합니다.
비활성화됨|토큰은 요청 URL에 정의되지 않은 쿼리 문자열 매개 변수로 지정될 수 있습니다.

**기본 동작:** 사용 안 함. 토큰은 요청 URL에 정의되지 않은 쿼리 문자열 매개 변수로 지정될 수 있습니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>URL 리디렉션
**목적:** Location 헤더를 통해 요청을 리디렉션합니다.

이 기능을 구성하려면 다음 옵션을 설정해야 합니다.

옵션|설명
-|-
코드|요청자에게 반환할 응답 코드를 선택합니다.
원본 및 패턴| 이러한 설정은 리디렉션될 수 있는 요청의 형식을 식별하는 요청 URI 패턴을 정의합니다. URL이 다음 기준을 모두 충족하는 요청만 리디렉션합니다. <br/> <br/> **원본 (또는 콘텐츠 액세스 지점):** 원본 서버를 식별 하는 상대 경로 선택 합니다. 이 경로는 _/XXXX/_ 섹션과 엔드포인트 이름입니다. <br/><br/> **원본 (패턴):** 상대 경로로 요청을 식별 하는 패턴을 정의 해야 합니다. 이 정규식 패턴에는 시작 직접 후 이전에 선택한 콘텐츠 액세스 지점 (위 참조) 하는 경로 정의 해야 합니다. <br/> - 이전에 정의한 요청 URI 기준(즉, 원본 및 패턴)이 이 기능에 대해 정의된 일치 조건과 충돌하지 않는지 확인합니다. <br/> - 패턴을 지정합니다. 패턴으로 빈 값을 사용하는 경우 모든 문자열이 일치됩니다.
대상| 위의 요청을 리디렉션할 URL을 정의합니다. <br/><br/> 다음을 사용하여 이 URL을 동적으로 구성합니다. <br/> -정규식 패턴 <br/>- [HTTP 변수](cdn-http-variables.md) <br/><br/> $를 사용 하 여 대상 패턴으로 원본 패턴에서 캡처한 값으로 대체_n_ 여기서 _n_ 를 캡처한 순서에 따라 값을 식별 합니다. 예를 들어 $1은 원본 패턴에서 캡처한 첫 번째 값을 나타내고, $2는 두 번째 값을 나타냅니다. <br/> 
절대 URL을 사용 하는 것이 좋습니다. 상대 URL을 사용하면 CDN URL을 잘못된 경로로 리디렉션할 수 있습니다.

**샘플 시나리오**

이 예제에서는 기본 CDN URL(http:\//marketing.azureedge.net/brochures)로 확인되는 에지 CNAME URL을 리디렉션하는 방법을 보여 줍니다.

요청이 유효하면 이 기본 에지 CNAME URL(http:\//cdn.mydomain.com/resources)로 리디렉션됩니다.

이 URL 리디렉션은 다음 구성을 통해 수행할 수 있습니다. ![URL 리디렉션](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**주요 정보:**

- URL 리디렉션 기능은 리디렉션할 요청 URL을 정의합니다. 따라서 추가적인 일치 조건이 필요하지 않습니다. 일치 조건을 "Always"로 정의했지만 "marketing" 고객 원본의 "brochures" 폴더를 가리키는 요청만 리디렉션합니다. 
- 일치 하는 모든 요청은에 지 CNAME URL 대상 옵션에서 정의 리디렉션됩니다. 
    - 샘플 시나리오 #1: 
        - 샘플 요청(CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - 요청 URL(리디렉션 이후): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - 샘플 시나리오 2: 
        - 샘플 요청(에지 CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - 요청 URL(리디렉션 이후): http:\//cdn.mydomain.com/resources/widgets.pdf
    - 샘플 시나리오 #3: 
        - 샘플 요청(에지 CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - 요청 URL(리디렉션 이후): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- 요청 체계 (% {scheme}) 변수는 리디렉션 후 요청의 체계가 변경 되지 있는지을 보장 하는 대상 옵션에서 활용 됩니다.
- 요청에서 캡처한 URL 세그먼트는 "$1"을 통해 새 URL에 추가됩니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>URL 다시 쓰기
**목적:** 요청 URL을 다시 작성 합니다.

주요 정보:

- 이 기능을 구성하려면 다음 옵션을 설정해야 합니다.

옵션|설명
-|-
 원본 및 패턴 | 이러한 설정은 다시 쓸 수 있는 요청의 형식을 식별하는 요청 URI 패턴을 정의합니다. URL이 다음 기준을 모두 충족하는 요청만 다시 씁니다. <br/><br/>  - **원본 (또는 콘텐츠 액세스 지점):** 원본 서버를 식별 하는 상대 경로 선택 합니다. 이 경로는 _/XXXX/_ 섹션과 엔드포인트 이름입니다. <br/><br/> - **원본 (패턴):** 상대 경로로 요청을 식별 하는 패턴을 정의 해야 합니다. 이 정규식 패턴에는 시작 직접 후 이전에 선택한 콘텐츠 액세스 지점 (위 참조) 하는 경로 정의 해야 합니다. <br/> 이전에 정의한 요청 URI 기준(즉, 원본 및 패턴)이 이 기능에 대해 정의된 일치 조건과 충돌하지 않는지 확인합니다. 패턴을 지정합니다. 패턴으로 빈 값을 사용하는 경우 모든 문자열이 일치됩니다. 
 대상  |위의 요청을 다시 쓸 상대 URL을 다음과 같이 정의합니다. <br/>    1. 원본 서버를 식별 하는 콘텐츠 액세스 포인트를 선택 합니다. <br/>    2. 다음을 사용하여 상대 경로 정의 <br/>        -정규식 패턴 <br/>        - [HTTP 변수](cdn-http-variables.md) <br/> <br/> $를 사용 하 여 대상 패턴으로 원본 패턴에서 캡처한 값으로 대체_n_ 여기서 _n_ 를 캡처한 순서에 따라 값을 식별 합니다. 예를 들어 $1은 원본 패턴에서 캡처한 첫 번째 값을 나타내고, $2는 두 번째 값을 나타냅니다. 

 이 기능을 사용하면 POP에서 기존의 리디렉션을 수행하지 않고도 URL을 다시 쓸 수 있습니다. 즉 요청자가 다시 쓴 URL을 요청한 것처럼 동일한 응답 코드를 받습니다.

**샘플 시나리오 1**

이 예제에서는 기본 CDN URL(http:\//marketing.azureedge.net/brochures/)로 확인되는 에지 CNAME URL을 리디렉션하는 방법을 보여 줍니다.

요청이 유효하면 이 기본 에지 CNAME URL(\//MyOrigin.azureedge.net/resources/)로 리디렉션됩니다.

이 URL 리디렉션은 다음 구성을 통해 수행할 수 있습니다. ![URL 리디렉션](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**샘플 시나리오 2**

이 예제에서는 정규식을 사용하여 에지 CNAME URL을 대문자에서 소문자로 리디렉션하는 방법을 보여 줍니다.

이 URL 리디렉션은 다음 구성을 통해 수행할 수 있습니다. ![URL 리디렉션](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**주요 정보:**

- URL 다시 쓰기 기능은 다시 쓸 요청 URL을 정의합니다. 따라서 추가적인 일치 조건이 필요하지 않습니다. 일치 조건을 "Always"로 정의했지만 "marketing" 고객 원본의 "brochures" 폴더를 가리키는 요청만 다시 씁니다.

- 요청에서 캡처한 URL 세그먼트는 "$1"을 통해 새 URL에 추가됩니다.

#### <a name="compatibility"></a>호환성
이 기능에는 요청에 적용하기 전에 충족되어야 하는 일치 조건이 포함됩니다. 충돌하는 일치 기준을 설정하지 않도록 방지하기 위해 이 기능은 다음 일치 조건과 호환되지 않습니다.

- AS 숫자
- CDN 원본
- 클라이언트 IP 주소
- 고객 원본
- 요청 스키마
- URL 경로 디렉터리
- URL 경로 확장
- URL 경로 파일 이름
- URL 경로 리터럴
- URL 경로 Regex
- URL 경로 와일드카드
- URL 쿼리 리터럴
- URL 쿼리 매개 변수
- URL 쿼리 Regex
- URL 쿼리 와일드카드

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>User 변수
**목적:** 내부용 으로만 사용 합니다.

[맨 위로 이동](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>다음 단계
* [규칙 엔진 참조](cdn-rules-engine-reference.md)
* [규칙 엔진 조건식](cdn-rules-engine-reference-conditional-expressions.md)
* [규칙 엔진 일치 조건](cdn-rules-engine-reference-match-conditions.md)
* [규칙 엔진을 사용하여 HTTP 동작 재정의](cdn-rules-engine.md)
* [Azure CDN 개요](cdn-overview.md)
