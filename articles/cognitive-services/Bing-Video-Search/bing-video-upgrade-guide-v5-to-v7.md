---
title: Bing Video API v5를 v7으로 업그레이드 | Microsoft Docs
description: 버전 7을 사용하도록 업데이트해야 하는 응용 프로그램의 부분을 식별합니다.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373079"
---
# <a name="video-search-api-upgrade-guide"></a>Video Search API 업그레이드 가이드

이 업그레이드 가이드는 Bing Video Search API의 버전 5와 버전 7 사이의 변경 내용을 식별합니다. 이 가이드를 사용하면 버전 7을 사용하도록 업데이트해야 하는 응용 프로그램의 부분을 식별하는 데 유용합니다.

## <a name="breaking-changes"></a>주요 변경 내용

### <a name="endpoints"></a>Endpoints

- 엔드포인트의 버전 번호가 v5에서 v7로 변경되었습니다. 예: https://api.cognitive.microsoft.com/bing/\*\*v7.0**/videos/search.

### <a name="error-response-objects-and-error-codes"></a>오류 응답 개체 및 오류 코드

- 모든 실패한 요청은 이제 응답 본문에서 `ErrorResponse` 개체를 포함해야 합니다.

- 다음 필드가 `Error` 개체에 추가되었습니다.  
  - `subCode`&mdash;가능한 경우 불연속 버킷으로 오류 코드 분할
  - `moreDetails`&mdash;`message`필드에 설명된 오류에 대한 추가 정보
   

- v5 오류 코드가 다음과 같이 가능한 `code` 및 `subCode` 값으로 바뀌었습니다.

|코드|SubCode|설명
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing은 하위 코드 조건 중 하나가 발생할 때마다 ServerError를 반환합니다. 응답은 HTTP 상태 코드가 500인 경우 이러한 오류를 포함합니다.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|Bing은 요청의 일부가 올바르지 않을 때마다 InvalidRequest를 반환합니다. 예를 들어 필수 매개 변수가 누락되었거나 매개 변수 값이 올바르지 않습니다.<br/><br/>오류가 ParameterMissing 또는 ParameterInvalidValue인 경우 오류인 경우 HTTP 상태 코드는 400입니다.<br/><br/>오류가 HttpNotAllowed이면 HTTP 상태 코드는 410입니다.
|RateLimitExceeded||Bing은 QPS(초당 쿼리) 또는 QPM(월별 쿼리) 할당량을 초과할 때마다 RateLimitExceeded를 반환합니다.<br/><br/>Bing은 QPS를 초과하면 HTTP 상태 코드 429를, QPM을 초과하는 경우 HTTP 상태 코드 403을 반환합니다.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing이 호출자를 인증할 수 없는 경우 Bing은 InvalidAuthorization을 반환합니다. 예를 들어 `Ocp-Apim-Subscription-Key` 헤더가 누락되었거나 구독 키가 올바르지 않습니다.<br/><br/>둘 이상의 인증 방법을 지정한 경우 중복이 발생합니다.<br/><br/>오류가 InvalidAuthorization이면 HTTP 상태 코드는 401입니다.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|리소스에 액세스할 수 있는 사용 권한이 호출자에게 없는 경우 Bing은 InsufficientAuthorization을 반환합니다. 이는 구독 키가 사용하지 않도록 설정되거나 만료된 경우 발생할 수 있습니다. <br/><br/>오류가 InsufficientAuthorization이면 HTTP 상태 코드는 403입니다.

- 다음은 이전 오류 코드를 새 코드에 매핑합니다. v5 오류 코드에서 종속성을 얻은 경우, 코드를 적절하게 업데이트합니다.

|버전 5 코드|버전 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
사용 안 함|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blocked|InvalidRequest.Blocked

### <a name="query-parameters"></a>쿼리 매개 변수

- `modulesRequested` 쿼리 매개 변수의 이름을 [모듈](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested)로 변경했습니다.  

### <a name="object-changes"></a>개체 변경 내용

- [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos)의 `nextOffsetAddCount` 필드 이름을 `nextOffset`으로 변경했습니다. 오프셋을 사용하는 방식도 변경되었습니다. 앞에서는 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 쿼리 매개 변수를 `nextOffset` 값에 이전 오프셋 값과 결과의 비디오 수를 더한 값으로 설정했습니다. 이번에는 간단하게 `offset` 쿼리 매개 변수를 `nextOffset` 값으로 설정했습니다.  
  
- `relatedVideos` 필드의 데이터 형식을 `Video[]`에서 [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule)로 변경했습니다([VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails) 참조).

