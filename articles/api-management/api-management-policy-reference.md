---
title: "Azure API 관리 정책 참조"
description: "API 관리를 구성하기 위해 사용할 수 있는 정책에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Azure API 관리 정책 참조
이 섹션에서는 [API Management 정책 참조][API Management policy reference]에 있는 정책에 대한 인덱스를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책][Policies in API Management]을 참조하세요.

정책이 다르게 지정하지 않는 한 정책 식은 어떤 API 관리 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름][Control flow] 및 [변수 설정][Set variable] 정책 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책][Advanced policies] 및 [정책 식][Policy expressions]을 참조하세요.

## <a name="policy-reference-index"></a>정책 참조 인덱스
* [액세스 제한 정책][Access restriction policies]
  * [HTTP 헤더 확인][Check HTTP header] - HTTP 헤더의 존재 및/또는 값을 적용합니다.
  * [구독으로 호출 속도 제한][Limit call rate by subscription] - 구독을 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
  * [키로 호출 속도 제한](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - 키를 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
  * [호출자 IP 제한][Restrict caller IPs] - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.
  * [구독으로 사용 할당량 설정][Set usage quota by subscription] - 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
  * [키로 사용 할당량 설정](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
  * [JWT 유효성 검사][Validate JWT] - 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.
* [고급 정책][Advanced policies]
  * [제어 흐름][Control flow] - 부울 [식][expressions]의 평가 결과에 따라 정책 문을 조건부로 적용합니다.
  * [요청 전달][Forward request] - 백 엔드 서비스에 요청을 전달합니다.
  * [이벤트 허브에 기록][Log to Event Hub] - [로거](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) 엔터티가 정의한 메시지 대상에 지정된 형식으로 메시지를 보냅니다.
  * [다시 시도](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - 조건이 충족될 때까지 포함된 정책 문을 실행하도록 다시 시도합니다. 실행은 지정된 시간 간격으로 최대 지정된 재시도 횟수까지 반복됩니다.
  * [응답 반환](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - 파이프라인 실행을 중단하고 호출자에게 직접 지정된 응답을 반환합니다.
  * [단방향 요청 전송](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - 지정된 URL에 대한 응답을 기다리지 않고 요청을 보냅니다.
  * [요청 전송](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - 지정된 URL로 요청을 보냅니다.
  * [요청 메서드 설정](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - 요청에 대한 HTTP 메서드를 변경할 수 있습니다.
  * [상태 설정](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - 지정된 값으로 HTTP 상태 코드를 변경합니다.
  * [변수 설정][Set variable] - 나중에 액세스할 수 있도록 명명된 [context][context] 변수의 값을 유지합니다.
  * [추적](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - [API 검사기](api-management-howto-api-inspector.md) 출력에 문자열을 추가합니다.
  * [대기](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - 계속하기 전에 완료할 포함된 요청 전송, 캐시에서 값 가져오기 또는 흐름 제어 정책 등을 기다립니다.
* [인증 정책][Authentication policies]
  * [기본 사용 인증][Authenticate with Basic] - 기본 인증을 사용하여 백 엔드 서비스를 인증합니다.
  * [클라이언트 인증서 사용 인증][Authenticate with client certificate] - 클라이언트 인증서를 사용하여 백 엔드 서비스를 인증합니다.
* [캐싱 정책][Caching policies] 
  * [캐시에서 가져오기][Get from cache] - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.
  * [캐시에 저장][Store to cache] - 지정된 캐시 제어 구성에 따라 응답을 캐시합니다.
  * [캐시에서 값 가져오기](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - 키로 캐시된 항목을 검색합니다.
  * [값을 캐시에 저장](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - 키로 캐시에 항목을 저장합니다.
  * [캐시에서 값을 제거](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - 키로 캐시에서 항목을 제거합니다.
* [도메인 간 정책][Cross domain policies] 
  * [도메인 간 호출 허용][Allow cross-domain calls] - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.
  * [CORS][CORS] - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
  * [JSONP][JSONP] - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
* [변환 정책][Transformation policies] 
  * [XML로 JSON 변환][Convert JSON to XML] - 요청 또는 응답 본문을 JSON에서 XML로 변환합니다.
  * [JSON으로 XML 변환][Convert XML to JSON] - 요청 또는 응답 본문을 XML에서 JSON으로 변환합니다.
  * [본문 문자열 찾기 및 바꾸기][Find and replace string in body] - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.
  * [콘텐츠의 URL 마스킹][Mask URLs in content] - 응답 본문의 링크를 다시 써서(마스킹) 게이트웨이를 통해 동일한 링크를 가리키도록 합니다.
  * [백 엔드 서비스 설정][Set backend service] - 들어오는 요청의 백 엔드 서비스를 변경합니다.
  * [본문 설정][Set body] - 들어오고 나가는 요청의 메시지 본문을 설정합니다.
  * [HTTP 헤더 설정][Set HTTP header] - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.
  * [쿼리 문자열 매개 변수 설정][Set query string parameter] - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.
  * [URL 다시 쓰기][Rewrite URL] - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.

## <a name="next-steps"></a>다음 단계
정책 식에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx


