---
title: Azure CDN 규칙 엔진에 대한 HTTP 변수 | Microsoft Docs
description: HTTP 변수를 사용하면 HTTP 요청 및 응답 메타데이터를 검색할 수 있습니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593809"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진에 대한 HTTP 변수
HTTP 변수는 HTTP 요청 및 응답 메타데이터를 검색할 수 있는 수단을 제공합니다. 이 메타데이터는 요청 또는 응답을 동적으로 변경하는 데 사용할 수 있습니다. HTTP 변수의 사용은 다음 규칙 엔진 기능으로 제한됩니다.

- [Cache-Key 다시 쓰기](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [클라이언트 요청 헤더 수정](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [클라이언트 응답 헤더 수정](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL 리디렉션](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL 다시 쓰기](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>정의
다음 표에 지원되는 HTTP 변수가 설명되어 있습니다. 지역 메타데이터(예: 우편 번호)를 특정 요청에 대해 사용할 수 없는 경우에는 빈 값이 반환됩니다.


| 이름 | 변수 | Description | 샘플 값 |
| ---- | -------- | ----------- | ------------ |
| ASN(요청자) | %{geo_asnum} | 요청자의 AS 번호를 나타냅니다. <br /><br />**사용되지 않음:** %{virt_dst_asnum}. <br />이 변수는 %{geo_asnum}에 대해 사용되지 않습니다. 이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다. | AS15133 |
| 도시(요청자) | %{geo_city} | 요청자의 도시를 나타냅니다. | Los Angeles |
| 대륙(요청자) | %{geo_continent} | 해당 약어를 통해 요청자의 대륙을 나타냅니다. <br />유효한 값은 <br />AF: 아프리카<br />AS: 아시아<br />EU: Europe<br />NA: North America<br />OC: 오세아니아<br />SA: 남미<br /><br />**사용되지 않음:** %{virt_dst_continent}. <br />% {Geo_continent (를) 대신이 변수는 사용 되지 합니다. <br />이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다.| 해당 사항 없음 |
| 쿠키 값 | %{cookie_Cookie} | 쿠키 용어에 의해 식별된 쿠키 키에 해당하는 값을 반환합니다. | 사용법 예제: <br />%{cookie__utma}<br /><br />샘플 값:<br />111662281.2.10.1222100123 |
| 국가(요청자) | %{geo_country} | 해당 국가 코드를 통해 요청자의 국가를 나타냅니다. <br />**사용되지 않음:** %{virt_dst_country}. <br /><br />이 변수는 %{geo_country}에 대해 사용되지 않습니다. 이 사용되지 않는 변수를 사용하는 규칙을 계속 사용할 수 있더라도, 새 변수를 사용하도록 업데이트해야 합니다. | US |
| 지정된 시장 지역(요청자) | %{geo_dma_code} |해당 지역 코드를 통해 요청자의 미디어 시장을 나타냅니다. <br /><br />이 필드는 미국에서 발생하는 요청에만 적용됩니다.| 745 |
| HTTP 요청 메서드 | %{request_method} | HTTP 요청 메서드를 나타냅니다. | 가져오기 |
| HTTP 상태 코드 | %{status} | 응답에 대한 HTTP 상태 코드를 나타냅니다. | 200 |
| IP 주소(요청자) | %{virt_dst_addr} | 요청자의 IP 주소를 나타냅니다. | 192.168.1.1 |
| 위도(요청자) | %{geo_latitude} | 요청자의 위도를 나타냅니다. | 34.0995 |
| 경도(요청자) | %{geo_longitude} | 요청자의 경도를 나타냅니다. | -118.4143 |
| 대도시 통계 지역(요청자) | %{geo_metro_code} | 요청자의 대도시 지역을 나타냅니다. <br /><br />이 필드는 미국에서 발생하는 요청에만 적용됩니다.<br />| 745 |
| 포트(요청자) | %{virt_dst_port} | 요청자의 임시 포트를 나타냅니다. | 55885 |
| 우편 번호(요청자) | %{geo_postal_code} | 요청자의 우편 번호를 나타냅니다. | 90210 |
| 쿼리 문자열 있음 | %{is_args} | 이 변수에 대한 값은 요청에 쿼리 문자열이 포함되어 있는지 여부에 따라 달라집니다.<br /><br />- 쿼리 문자열 있음: ?<br />-쿼리 문자열: NULL | ? |
| 쿼리 문자열 매개 변수 있음 | %{is_amp} | 이 변수에 대한 값은 요청에 최소한 하나의 쿼리 문자열 매개 변수가 포함되어 있는지 여부에 따라 달라집니다.<br /><br />- 매개 변수 있음: &<br />-매개 변수: NULL | & |
| 쿼리 문자열 매개 변수 값 | %{arg_&lt;parameter&gt;} | &lt;매개 변수&gt; 용어에 의해 식별되는 쿼리 문자열 매개 변수에 해당하는 값을 반환합니다. | 사용법 예제: <br />%{arg_language}<br /><br />샘플 쿼리 문자열 매개 변수: <br />?language=en<br /><br />샘플 값: en |
| 쿼리 문자열 값 | %{query_string} | 요청 URL에서 정의된 전체 쿼리 문자열 값을 나타냅니다. |key1=val1&key2=val2&key3=val3 |
| 참조자 도메인 | %{referring_domain} | 참조 페이지 요청 헤더에 정의 된 도메인을 나타냅니다. | <www.google.com> |
| 지역(요청자) | %{geo_region} | 영숫자의 약어를 통해 요청자의 지역(예: 시/도)을 나타냅니다. | CA |
| 요청 헤더 값 | %{http_RequestHeader} | RequestHeader 용어에 의해 식별되는 요청 헤더에 해당하는 값을 반환합니다. <br /><br />요청 헤더의 이름에 대시가 포함되는 경우(예: User-Agent) 밑줄로 바꿉니다(예: User_Agent).| 사용법 예제: %{http_Connection}<br /><br />샘플 값: 연결 유지 | 
| 요청 호스트 | %{host} | 요청 URL에 정의된 호스트를 나타냅니다. | <www.mydomain.com> |
| 요청 프로토콜 | %{request_protocol} | 요청의 프로토콜을 나타냅니다. | HTTP/1.1 |
| 요청 스키마 | %{scheme} | 요청 스키마를 나타냅니다. |http |
| 요청 URI(상대값) | %{request_uri} | 요청 URI에 정의된 쿼리 문자열을 포함하는 상대 경로를 나타냅니다. | /marketing/foo.js?loggedin=true |
| 요청 URI(쿼리 문자열을 사용하지 않는 상대값) | %{uri} | 요청된 콘텐츠에 대한 상대 경로를 나타냅니다. <br /><br/>주요 정보:<br />- 이 상대 경로는 쿼리 문자열을 제외합니다.<br />- 이 상대 경로는 URL 다시 작성을 반영합니다. URL은 다음과 같은 조건에서 다시 작성됩니다.<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL 다시 쓰기 기능: 이 기능은 요청 URI에서에서 정의 된 상대 경로 다시 작성 합니다.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;에 지 CNAME URL: 이 유형의 요청은 해당 CDN URL로 다시 작성 됩니다. |/800001/corigin/rewrittendir/foo.js |
| 요청 URI | %{request} | 요청을 설명합니다. <br />구문: &lt;HTTP 메서드&gt; &lt;상대 경로&gt; &lt;HTTP 프로토콜&gt; | /marketing/foo.js?loggedin=true HTTP/1.1 가져오기 |
| 응답 헤더 값 | %{resp_&lt;ResponseHeader&gt;} | &lt;ResponseHeader&gt; 용어에 의해 식별되는 응답 헤더에 해당하는 값을 반환합니다. <br /><br />응답 헤더의 이름에 대시가 포함되는 경우(예: User-Agent) 밑줄로 바꿉니다(예: User_Agent). | 사용법 예제: %{resp_Content_Length}<br /><br />샘플 값: 100 |

## <a name="usage"></a>사용법
다음 표에서 HTTP 변수를 지정하는 데 적절한 구문을 설명합니다.


| 구문 | 예제 | 설명 |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | 이 구문을 사용하여 지정된 &lt;HTTPVariable&gt;에 해당하는 전체 값을 가져옵니다. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | 이 구문을 사용하여 지정된 &lt;HTTPVariableDelimiter&gt;에 해당하는 전체 값에 관한 사례를 설정합니다. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | &lt;HTTPVariableDelimiterExpression&gt;에 대한 정규식을 사용하여 HTTP 변수의 값을 바꾸기, 삭제 또는 조작합니다. |

HTTP 변수 이름은 영문자 및 밑줄만 지원합니다. 지원되지 않는 문자는 밑줄로 변환합니다.

## <a name="delimiter-reference"></a>구분 기호 참조
구분 기호는 다음과 같은 효과를 수행하기 위해 HTTP 변수 뒤에 지정할 수 있습니다.

- 변수와 연결된 값을 변환합니다.

     예제: 전체 값을 소문자로 변환합니다.

- 변수와 연결된 값을 삭제합니다.

- 변수와 연결된 값을 조작합니다.

     예제: HTTP 변수를 사용 하 여 연결 된 값을 변경 하려면 정규식을 사용 합니다.

구분 기호는 다음 표에 설명되어 있습니다.

| 구분 기호 | Description |
| --------- | ----------- |
| := | 다음 중 하나인 경우 기본값이 변수에 할당될 것을 나타냅니다. <br />- 누락됨 <br />- NULL로 설정됨. |
| :+ | 값이 할당된 경우 기본값이 변수에 할당될 것을 나타냅니다. |
| : | 변수에 할당된 값의 하위 문자열이 확장될 것을 나타냅니다. |
| # | 변수와 연결된 값의 시작 부분에서 발견되면 이 구분 기호 뒤에 지정된 패턴을 삭제해야 함을 나타냅니다. |
| % | 변수와 연결된 값의 끝 부분에서 발견되면 이 구분 기호 뒤에 지정된 패턴을 삭제해야 함을 나타냅니다. <br />이 정의는 % 기호가 구분 기호로 사용되는 경우에만 적용됩니다. |
| / | HTTP 변수 또는 패턴을 구분합니다. |
| // | 지정된 패턴의 모든 인스턴스를 찾아서 바꿉니다. |
| /= | 지정된 패턴의 모든 발생을 찾고, 복사하고, 다시 작성합니다. |
| , | HTTP 변수와 연결된 값을 소문자로 변환합니다. |
| ^ | HTTP 변수와 연결된 값을 대문자로 변환합니다. |
| ,, | HTTP 변수와 연결된 값에서 지정된 문자의 모든 인스턴스를 소문자로 변환합니다. |
| ^^ | HTTP 변수와 연결된 값에서 지정된 문자의 모든 인스턴스를 대문자로 변환합니다. |

## <a name="exceptions"></a>예외
다음 표에서 지정된 텍스트는 HTTP 변수로 처리되지 않는 상황을 설명합니다.

| 조건 | Description | 예제 |
| --------- | ----------- | --------|
| % 기호 이스케이프 | 백슬래시를 사용하여 백분율 기호를 이스케이프할 수 있습니다. <br />오른쪽의 샘플 값은 HTTP 변수가 아닌 리터럴 값으로 처리됩니다.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| 알 수 없는 변수 | 빈 문자열은 항상 알 수 없는 변수에 대해 반환됩니다. | %{unknown_variable} |
| 잘못된 문자 또는 구문 | 잘못된 문자 또는 구문을 포함하는 변수는 리터럴 값으로 처리됩니다. <br /><br />예제 #1: 지정된 된 값에 잘못 된 문자 (예를 들어,-). <br /><br />예제 #2: 지정된 된 값에는 이중 중괄호 집합을 포함합니다. <br /><br />예제 #3: 지정된 된 값에 닫는 중괄호를 누락 되었습니다.<br /> | 예제 #1: %{resp_user-agent} <br /><br />예제 #2: %{{host}} <br /><br />예제 #3: %{host |
| 변수 이름 누락 | 변수를 지정하지 않은 경우 항상 NULL 값이 반환됩니다. | %{} |
| 후행 문자 | 변수를 추적하는 문자는 리터럴 값으로 처리됩니다. <br />오른쪽의 샘플 값에는 리터럴 값으로 처리되는 추적 중괄호가 포함되어 있습니다. | %{host}} |

## <a name="setting-default-header-values"></a>기본 헤더 값 설정
다음 조건 중 하나라도 충족할 경우 기본값을 헤더에 할당할 수 있습니다.
- 누락/설정 해제
- NULL로 설정됨.

다음 표에서 기본값을 정의하는 방법을 설명합니다.

| 조건 | 구문 | 예제 | 설명 |
| --------- | ------ | --------| ----------- |
| 다음 조건 중 하나라도 충족할 경우 헤더를 기본값으로 설정합니다. <br /><br />- 헤더 누락 <br /><br />- 헤더 값이 NULL로 설정됨.| %{Variable:=Value} | %{http_referrer:=unspecified} | 참조 페이지 헤더에만 설정할 *지정 되지 않은* 없거나 설정 때 NULL로 합니다. 설정된 경우 아무 작업도 수행되지 않습니다. |
| 누락된 경우 헤더를 기본값으로 설정합니다. | %{Variable=Value} | %{http_referrer=unspecified} | 참조 페이지 헤더에만 설정할 *지정 되지 않은* 누락 된 경우. 설정된 경우 아무 작업도 수행되지 않습니다. |
| 다음 조건 중 하나라도 충족하지 않을 경우 헤더를 기본값으로 설정합니다. <br /><br />- 누락됨<br /><br /> - NULL로 설정됨. | %{Variable:+Value} | %{http_referrer:+unspecified} | 참조 페이지 헤더에만 설정할 *지정 되지 않은* 경우 값에 할당 합니다. 누락되거나 NULL로 설정되지 않은 경우 아무 작업도 수행되지 않습니다. |

## <a name="manipulating-variables"></a>변수 조작
변수는 다음과 같은 방법으로 조작할 수 있습니다.

- 부분 문자열 확장
- 패턴 제거

### <a name="substring-expansion"></a>부분 문자열 확장
기본적으로 변수는 해당 전체 값으로 확장됩니다. 변수 값의 부분 문자열만 확장하려면 다음 구문을 사용합니다.

`%<Variable>:<Offset>:<Length>}`

주요 정보:

- 오프셋 용어에 할당된 값은 부분 문자열의 시작 문자를 결정합니다.

     - 양의: 부분 문자열의 시작 문자는 문자열의 첫 번째 문자에서 계산 됩니다.
     - 0: 부분 문자열의 시작 문자는 문자열의 첫 번째 문자입니다.
     - 음수: 부분 문자열의 시작 문자는 문자열의 마지막 문자에서 계산 됩니다.

- 부분 문자열의 길이는 *길이* 용어에 의해 결정됩니다.

     - 생략 됩니다. 길이 용어를 생략 하면 부분 문자열을 시작 문자 및 문자열의 끝 사이 모든 문자를 포함할 수 있습니다.
     - 양의: 오른쪽에서 시작 문자의 부분 문자열의 길이 결정합니다.
     - 음수: 시작 문자에서 왼쪽 부분 문자열의 길이 결정합니다.

#### <a name="example"></a>예제:

다음 예제에서는 다음 샘플 요청 URL를 사용합니다.

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

다음 문자열은 변수를 조작하기 위한 다양한 메서드를 보여 줍니다.

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

샘플 요청 URL에 따라 위의 변수 조작은 다음 값을 생성합니다.

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>패턴 제거
변수 값의 시작 부분이나 끝에서 특정 패턴에 일치하는 텍스트를 제거할 수 있습니다.

| 구문 | 작업 |
| ------ | ------ |
| %{Variable#Pattern} | 변수 값의 시작 부분에서 지정된 패턴이 발견되면 텍스트를 제거합니다. |
| %{Variable%Pattern} | 변수 값의 마지막 부분에서 지정된 패턴이 발견되면 텍스트를 제거합니다. |

#### <a name="example"></a>예제:

이 샘플 시나리오에서 *request_uri* 변수는 다음으로 설정됩니다.

`/800001/myorigin/marketing/product.html?language=en-US`

다음 표에서 이 구문의 작동 방식을 설명합니다.

| 샘플 구문 | 결과 | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | 변수가 패턴으로 시작하기 때문에 대체되었습니다. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | 변수가 패턴으로 끝나지 않으므로 변경된 내용이 없습니다.|

### <a name="find-and-replace"></a>찾기 및 바꾸기
찾기 및 바꾸기 구문은 다음 표에 설명되어 있습니다.

| 구문 | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | 지정된 패턴의 첫 번째 발생을 찾아서 바꿉니다. |
| %{Variable//Find/Replace} | 지정된 패턴의 모든 발생을 찾아서 바꿉니다. |
| %{Variable^} |전체 값을 대문자로 변환합니다. |
| %{Variable^Find} | 지정된 패턴의 첫 번째 발생을 대문자로 변환합니다. |
| %{Variable,} | 전체 값을 소문자로 변환합니다. |
| %{Variable,Find} | 지정된 패턴의 첫 번째 발생을 소문자로 변환합니다. |

### <a name="find-and-rewrite"></a>찾기 및 다시 쓰기
찾기 및 바꾸기에 대한 변형의 경우 다시 작성하는 경우 지정된 패턴과 일치하는 텍스트를 사용합니다. 찾기 및 다시 쓰기 구문은 다음 표에 설명되어 있습니다.

| 구문 | 작업 |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 지정된 패턴의 모든 발생을 찾고, 복사하고, 다시 작성합니다. |
| %{Variable/^Find/Rewrite} | 변수의 시작에서 발생하는 경우 지정된 패턴을 찾기, 복사 및 다시 씁니다. |
| %{Variable/$Find/Rewrite} | 변수의 마지막에서 발생하는 경우 지정된 패턴을 찾기, 복사 및 다시 씁니다. |
| %{Variable/Find} | 지정된 패턴의 모든 발생을 찾아서 삭제합니다. |

주요 정보:

- 정수 뒤에 달러 기호를 지정하여(예: $1) 지정된 패턴과 일치하는 텍스트를 확장합니다.

- 여러 개의 패턴을 지정할 수 있습니다. 패턴이 지정되는 순서는 할당되는 정수를 결정합니다. 다음 예제에서는 첫 번째 패턴이 “www.”에 일치하며 두 번째 패턴은 두 번째 수준 도메인에 일치하고 세 번째 패턴은 최상위 도메인에 일치합니다.

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- 다시 쓴 값은 텍스트 및 이러한 자리 표시자의 조합으로 구성할 수 있습니다.

    이전 예제에서는 `cdn.$2.$3:80`에 호스트 이름을 다시 씁니다(예: cdn.mydomain.com:80).

- 패턴 자리 표시자의 경우(예: $1) 다음 플래그를 통해 수정할 수 있습니다.
     - U: 확장된 값을 대문자입니다.

         샘플 구문:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: 확장된 값을 소문자입니다.

         샘플 구문:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- 연산자는 패턴 앞에 지정되어야 합니다. 지정된 연산자는 패턴 캡처 동작을 결정합니다.

     - `=`: 지정 된 패턴의 모든 항목을 캡처하고 다시 작성할 수 있어야 합니다를 나타냅니다.
     - `^`: 지정된 된 패턴을 사용 하 여 시작 하는 텍스트만 캡처된다는 것을 나타냅니다.
     - `$`: 지정된 된 패턴으로 끝나는 텍스트만 캡처 된다는 것을 나타냅니다.
 
- */Rewrite* 값을 생략하면 패턴과 일치하는 텍스트가 삭제됩니다.


