---
title: Azure API 관리 정책 참조
description: API 관리를 구성하기 위해 사용할 수 있는 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: apimpm

---
# Azure API 관리 정책 참조
이 섹션에서는 [API 관리 정책 참조][API 관리 정책 참조]에 있는 정책에 대한 인덱스를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API 관리 정책][API 관리 정책]을 참조하세요.

정책이 다르게 지정하지 않는 한 정책 식은 어떤 API 관리 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름][제어 흐름] 및 [변수 설정][변수 설정] 정책 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책][고급 정책] 및 [정책 식][정책 식]을 참조하세요.

## 정책 참조 인덱스
* [액세스 제한 정책][액세스 제한 정책]
  * [HTTP 헤더 확인][HTTP 헤더 확인] - HTTP 헤더의 존재 및/또는 값을 적용합니다.
  * [구독으로 호출 속도 제한][구독으로 호출 속도 제한] - 구독을 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
  * [키로 호출 속도 제한](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - 키를 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
  * [호출자 IP 제한][호출자 IP 제한] - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.
  * [구독으로 사용 할당량 설정][구독으로 사용 할당량 설정] - 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
  * [키로 사용 할당량 설정](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
  * [JWT 유효성 검사][JWT 유효성 검사] - 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.
* [고급 정책][고급 정책]
  * [흐름 제어][흐름 제어] - 부울 [식][식]의 평가 결과에 따라 정책 문을 조건부로 적용합니다.
  * [요청 전달][요청 전달] - 백 엔드 서비스에 요청을 전달합니다.
  * [이벤트 허브에 기록][이벤트 허브에 기록] - [로거](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) 엔터티가 정의한 메시지 대상에 지정된 형식으로 메시지를 보냅니다.
  * [다시 시도](https://msdn.microsoft.com/ko-KR/library/dn894085.aspx#Retry) - 조건이 충족될 때까지 포함된 정책 문을 실행하도록 다시 시도합니다. 실행은 지정된 시간 간격으로 최대 지정된 다시 시도 횟수까지 반복됩니다.
  * [응답 반환](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - 파이프라인 실행을 중단하고 호출자에게 직접 지정된 응답을 반환합니다.
  * [단방향 요청 전송](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - 지정된 URL에 대한 응답을 기다리지 않고 요청을 보냅니다.
  * [요청 전송](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - 지정된 URL로 요청을 보냅니다.
  * [요청 메서드 설정](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - 요청에 대한 HTTP 메서드를 변경할 수 있습니다.
  * [상태 설정](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - 지정된 값으로 HTTP 상태 코드를 변경합니다.
  * [변수 설정][변수 설정] - 나중에 액세스할 수 있도록 명명된 [context][context] 변수의 값을 유지합니다.
  * [추적](https://msdn.microsoft.com/ko-KR/library/dn894085.aspx#Trace) - [API 검사기](api-management-howto-api-inspector.md) 출력에 문자열을 추가합니다.
  * [대기](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - 계속하기 전에 완료할 포함된 요청 전송, 캐시에서 값 가져오기 또는 흐름 제어 정책 등을 기다립니다.
* [인증 정책][인증 정책]
  * [기본 사용 인증][기본 사용 인증] - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.
  * [클라이언트 인증서 사용 인증][클라이언트 인증서 사용 인증] - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.
* [캐싱 정책][캐싱 정책]
  * [캐시에서 가져오기][캐시에서 가져오기] - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.
  * [캐시에 저장][캐시에 저장] - 지정된 캐시 제어 구성에 따라 응답을 캐시합니다.
  * [캐시에서 값 가져오기](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - 키로 캐시된 항목을 검색합니다.
  * [값을 캐시에 저장](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - 키로 캐시에 항목을 저장합니다.
  * [캐시에서 값을 제거](https://msdn.microsoft.com/ko-KR/library/dn894086.aspx#RemoveCacheByKey) - 키로 캐시에서 항목을 제거합니다.
* [도메인 간 정책][도메인 간 정책]
  * [도메인 간 호출 허용][도메인 간 호출 허용] - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.
  * [CORS][CORS] - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
  * [JSONP][JSONP] - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
* [변환 정책][변환 정책]
  * [XML로 JSON 변환][XML로 JSON 변환] - 요청 또는 응답 본문을 JSON에서 XML로 변환합니다.
  * [JSON으로 XML 변환][JSON으로 XML 변환] - 요청 또는 응답 본문을 XML에서 JSON으로 변환합니다.
  * [본문 문자열 찾기 및 바꾸기][본문 문자열 찾기 및 바꾸기] - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.
  * [콘텐츠의 URL 마스킹][콘텐츠의 URL 마스킹] - 응답 본문의 링크를 다시 써서(마스킹) 게이트웨이를 통해 동일한 링크를 가리키도록 합니다.
  * [백 엔드 서비스 설정][백 엔드 서비스 설정] - 들어오는 요청의 백 엔드 서비스를 변경합니다.
  * [본문 설정][본문 설정] -들어오고 나가는 요청의 메시지 본문을 설정합니다.
  * [HTTP 헤더 설정][HTTP 헤더 설정] - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.
  * [쿼리 문자열 매개 변수 설정][쿼리 문자열 매개 변수 설정] - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.
  * [URL 다시 쓰기][URL 다시 쓰기] - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.

## 다음 단계
정책 식에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[액세스 제한 정책]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[HTTP 헤더 확인]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[구독으로 호출 속도 제한]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[호출자 IP 제한]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[구독으로 사용 할당량 설정]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[JWT 유효성 검사]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[고급 정책]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[제어 흐름]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[흐름 제어]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[변수 설정]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[식]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[요청 전달]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[이벤트 허브에 기록]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[인증 정책]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[기본 사용 인증]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[클라이언트 인증서 사용 인증]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[캐싱 정책]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[캐시에서 가져오기]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[캐시에 저장]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[도메인 간 정책]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[도메인 간 호출 허용]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[변환 정책]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[XML로 JSON 변환]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[JSON으로 XML 변환]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[본문 문자열 찾기 및 바꾸기]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[콘텐츠의 URL 마스킹]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[백 엔드 서비스 설정]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[본문 설정]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[HTTP 헤더 설정]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[쿼리 문자열 매개 변수 설정]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL 다시 쓰기]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[API 관리 정책]: api-management-howto-policies.md
[API 관리 정책 참조]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[정책 식]: https://msdn.microsoft.com/library/azure/dn910913.aspx



<!---HONumber=AcomDC_0831_2016-->