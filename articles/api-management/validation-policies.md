---
title: Azure API Management 유효성 검사 정책 | Microsoft Docs
description: Azure API Management에서 요청 및 응답의 유효성을 검사 하는 데 사용할 수 있는 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 3f91ca21512b8cddcac7fe71fa3eec07e1a8745a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720086"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>요청 및 응답의 유효성을 검사 하 API Management 정책

이 문서에서는 다음 API Management 정책에 대 한 참조를 제공 합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

유효성 검사 정책을 사용 하 여 OpenAPI 스키마에 대 한 API 요청 및 응답의 유효성을 검사 하 고 헤더 또는 페이로드 삽입과 같은 취약점 으로부터 보호 합니다. 웹 응용 프로그램 방화벽을 대체 하는 것은 아니지만 유효성 검사 정책은 정적의 미리 정의 된 규칙을 사용 하는 보안 제품에 포함 되지 않는 추가 위협 클래스에 대응할 수 있는 유연성을 제공 합니다.

## <a name="validation-policies"></a>정책 유효성 검사

- [콘텐츠 유효성 검사](#validate-content) -API 스키마에 대 한 요청 또는 응답 본문의 크기 또는 JSON 스키마의 유효성을 검사 합니다. 
- [매개 변수 유효성 검사](#validate-parameters) -API 스키마에 대 한 요청 헤더, 쿼리 또는 경로 매개 변수의 유효성을 검사 합니다.
- [헤더 유효성 검사](#validate-headers) -API 스키마에 대해 응답 헤더의 유효성을 검사 합니다.
- [상태 코드 유효성 검사](#validate-status-code) -API 스키마에 대 한 응답에서 HTTP 상태 코드의 유효성을 검사 합니다.

> [!NOTE]
> 유효성 검사 정책에서 사용할 수 있는 API 스키마의 최대 크기는 4mb입니다. 스키마가이 제한을 초과 하는 경우 유효성 검사 정책은 런타임에 오류를 반환 합니다. 이를 늘리려면 [지원](https://azure.microsoft.com/support/options/)담당자에 게 문의 하세요. 

## <a name="actions"></a>작업

각 유효성 검사 정책에는 api 요청 또는 API 스키마에 대 한 응답에서 엔터티의 유효성을 검사할 때 API Management 작업을 지정 하는 특성이 포함 되어 있습니다. Api 스키마에 표시 되는 요소에 대 한 동작을 지정할 수 있으며, API 스키마에 표시 되지 않는 요소에 대해 정책에 따라 동작을 지정할 수 있습니다. 정책의 자식 요소에 지정 된 작업은 부모에 대해 지정 된 동작을 재정의 합니다.

사용 가능한 작업:

| 작업         | Description          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | 유효성 검사를 건너뜁니다. |
| 방지 | 요청 또는 응답 처리를 차단 하 고, 자세한 유효성 검사 오류를 기록 하 고, 오류를 반환 합니다. 첫 번째 오류 집합이 검색 되 면 처리가 중단 됩니다. |
| 검색(detect) | 요청 또는 응답 처리를 중단 하지 않고 유효성 검사 오류를 기록 합니다. |

## <a name="logs"></a>로그

정책 실행 중의 유효성 검사 오류에 대 한 세부 정보는 `context.Variables` `errors-variable-name` 정책의 루트 요소에 있는 특성에 지정 된의 변수에 기록 됩니다. 작업에 구성 된 경우 `prevent` 유효성 검사 오류가 발생 하면 추가 요청 또는 응답 처리가 차단 되 고 속성에도 전파 됩니다 `context.LastError` . 

오류를 조사 하려면 [추적](api-management-advanced-policies.md#Trace) 정책을 사용 하 여 [Application Insights](api-management-howto-app-insights.md)컨텍스트 변수에서 오류를 기록 합니다.

## <a name="performance-implications"></a>성능 의미

유효성 검사 정책을 추가 하면 API 처리량에 영향을 줄 수 있습니다. 다음과 같은 일반적인 원칙이 적용 됩니다.
* API 스키마 크기가 클수록 처리량이 낮아집니다. 
* 요청이 나 응답에서 페이로드가 클수록 처리량이 낮아집니다. 
* API 스키마의 크기가 페이로드의 크기 보다 성능에 더 큰 영향을 줍니다. 
* 몇 메가바이트 크기의 API 스키마에 대해 유효성 검사를 수행 하면 일부 조건에서 요청 또는 응답 시간 초과가 발생할 수 있습니다. 효과는 서비스의  **소비** 및 **개발자** 계층에서 더 두드러지게 나타납니다. 

API 처리량에 대 한 유효성 검사 정책의 영향을 평가 하기 위해 예상 되는 프로덕션 워크 로드에서 부하 테스트를 수행 하는 것이 좋습니다.

## <a name="validate-content"></a>콘텐츠 유효성 검사

`validate-content`정책은 API 스키마에 대 한 요청 또는 응답 본문의 크기 또는 JSON 스키마의 유효성을 검사 합니다. JSON 이외의 형식은 지원 되지 않습니다.

### <a name="policy-statement"></a>정책 문

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>예제

다음 예제에서 요청 및 응답의 JSON 페이로드는 검색 모드에서 유효성이 검사 됩니다. 페이로드가 100 보다 큰 메시지는 차단 됩니다. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 유효성 검사-콘텐츠 | 루트 요소입니다.                                                                                                                               | 예      |
| 콘텐츠 | 이러한 요소 중 하나 이상을 추가 하 여 요청 또는 응답의 콘텐츠 형식에 대 한 유효성을 검사 하 고 지정 된 작업을 수행 합니다.  | 아니요 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 지정 되지 않음-content-type-동작 | API 스키마에 지정 되지 않은 콘텐츠 형식을 사용 하 여 요청 또는 응답에 대해 수행할 [작업](#actions) 입니다. |  예     | 해당 없음   |
| 최대 크기 | 헤더에 대해 확인 된 요청 또는 응답 본문의 최대 길이 (바이트)입니다 `Content-Length` . 요청 본문 또는 응답 본문이 압축 된 경우이 값은 압축을 푼 길이입니다. 허용 되는 최대 값: 102400 바이트 (100 KB).  | 예       | 해당 없음   |
| 크기 초과-동작 | 본문이에 지정 된 크기를 초과 하는 요청 또는 응답에 대해 수행할 [작업](#actions) 입니다 `max-size` . |  예     | 해당 없음   |
| 오류-변수 이름 | `context.Variables`유효성 검사 오류를 기록할의 변수 이름입니다.  |   예    | 해당 없음   |
| 형식 | 헤더에 대해 확인 된 본문 유효성 검사를 실행할 내용 유형 `Content-Type` 입니다. 이 값은 대/소문자를 구분 하지 않습니다. 비어 있는 경우 API 스키마에 지정 된 모든 콘텐츠 형식에 적용 됩니다. |   아니요    |  해당 없음  |
| 유효성 검사 | 일치 하는 콘텐츠 형식의 요청 또는 응답 본문의 유효성 검사에 사용할 유효성 검사 엔진입니다. 현재 유일 하 게 지원 되는 값은 "json"입니다.   |  예     |  해당 없음  |
| 작업 | 본문이 지정 된 콘텐츠 형식과 일치 하지 않는 요청이 나 응답에 대해 수행할 [동작](#actions) 입니다.  |  예      | 해당 없음   |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, on-error

-   **정책 범위:** 모든 범위

## <a name="validate-parameters"></a>매개 변수 유효성 검사

`validate-parameters`정책은 API 스키마에 대 한 요청의 헤더, 쿼리 또는 경로 매개 변수의 유효성을 검사 합니다.

> [!IMPORTANT]
> 이전 버전의 관리 API를 사용 하 여 API를 가져온 경우 `2021-01-01-preview` `validate-parameters` 정책이 작동 하지 않을 수 있습니다. Management API 버전 이상을 사용 하 여 API를 다시 가져와야 할 수 있습니다 `2021-01-01-preview` .


### <a name="policy-statement"></a>정책 문

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>예제

이 예에서 모든 쿼리 및 경로 매개 변수의 유효성은 방지 모드와 검색 모드의 헤더에서 확인 됩니다. 유효성 검사는 여러 헤더 매개 변수에 대해 재정의 됩니다.

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
</validate-parameters>
```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 유효성 검사 매개 변수 | 루트 요소입니다. 요청의 모든 매개 변수에 대 한 기본 유효성 검사 동작을 지정 합니다.                                                                                                                              | 예      |
| headers | 요청에서 헤더 매개 변수에 대 한 기본 유효성 검사 작업을 재정의 하려면이 요소를 추가 합니다.   | 예 |
| Query | 요청에서 쿼리 매개 변수에 대 한 기본 유효성 검사 동작을 재정의 하려면이 요소를 추가 합니다.  | 예 |
| 경로 | 요청에서 URL 경로 매개 변수에 대 한 기본 유효성 검사 동작을 재정의 하려면이 요소를 추가 합니다.  | 예 |
| 매개 변수(parameter) | 명명 된 매개 변수에 대 한 요소를 하나 이상 추가 하 여 유효성 검사 동작의 상위 수준 구성을 재정의 합니다. | 아니요 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 지정 된 매개 변수-동작 | API 스키마에 지정 된 요청 매개 변수에 대해 수행할 [작업](#actions) 입니다. <br/><br/> , 또는 요소에서 제공 되는 경우 `headers` `query` `path` 값은 요소의 값을 재정의 합니다 `specified-parameter-action` `validate-parameters` .  |  예     | 해당 없음   |
| 지정 되지 않은 매개 변수-동작 | API 스키마에 지정 되지 않은 요청 매개 변수에 대해 수행할 [작업](#actions) 입니다. <br/><br/>또는 요소에서 제공 되는 경우 `headers` `query` 값은 요소의 값을 재정의 합니다 `unspecified-parameter-action` `validate-parameters` . |  예     | 해당 없음   |
| 오류-변수 이름 | `context.Variables`유효성 검사 오류를 기록할의 변수 이름입니다.  |   예    | 해당 없음   |
| name | 유효성 검사 동작을 재정의할 매개 변수의 이름입니다. 이 값은 대/소문자를 구분 하지 않습니다.  | 예 | 해당 없음 |
| 작업 | 이름이 일치 하는 매개 변수에 대해 수행할 [동작](#actions) 입니다. 매개 변수가 API 스키마에 지정 된 경우이 값은 상위 수준 구성을 재정의 합니다 `specified-parameter-action` . API 스키마에 매개 변수가 지정 되지 않은 경우이 값은 상위 수준 구성을 재정의 합니다 `unspecified-parameter-action` .| 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="validate-headers"></a>헤더 유효성 검사

`validate-headers`정책은 API 스키마에 대해 응답 헤더의 유효성을 검사 합니다.

> [!IMPORTANT]
> 이전 버전의 관리 API를 사용 하 여 API를 가져온 경우 `2021-01-01-preview` `validate-headers` 정책이 작동 하지 않을 수 있습니다. Management API 버전 이상을 사용 하 여 API를 다시 가져와야 할 수 있습니다 `2021-01-01-preview` .

### <a name="policy-statement"></a>정책 문

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>예제

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 헤더 유효성 검사 | 루트 요소입니다. 응답의 모든 헤더에 대 한 기본 유효성 검사 동작을 지정 합니다.                                                                                                                              | 예      |
| header | 명명 된 헤더에 대 한 하나 이상의 요소를 추가 하 여 응답의 헤더에 대 한 기본 유효성 검사 동작을 재정의 합니다. | 아니요 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 지정 된 헤더 동작 | API 스키마에 지정 된 응답 헤더에 대해 수행할 [작업](#actions) 입니다.  |  예     | 해당 없음   |
| 지정 되지 않은-헤더-동작 | API 스키마에 지정 되지 않은 응답 헤더에 대해 수행할 [작업](#actions) 입니다.  |  예     | 해당 없음   |
| 오류-변수 이름 | `context.Variables`유효성 검사 오류를 기록할의 변수 이름입니다.  |   예    | 해당 없음   |
| name | 유효성 검사 동작을 재정의할 헤더의 이름입니다. 이 값은 대/소문자를 구분 하지 않습니다. | 예 | 해당 없음 |
| 작업 | 이름이 일치 하는 헤더에 대해 수행할 [작업](#actions) 입니다. 헤더가 API 스키마에 지정 된 경우이 값은 요소의 값을 재정의 `specified-header-action` `validate-headers` 합니다. 그렇지 않으면 `unspecified-header-action` 헤더 유효성 검사 요소에서 값을 재정의 합니다. | 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** 아웃 바운드, 오류 발생 시

-   **정책 범위:** 모든 범위

## <a name="validate-status-code"></a>상태 코드 유효성 검사

`validate-status-code`정책은 API 스키마에 대 한 응답에서 HTTP 상태 코드의 유효성을 검사 합니다. 이 정책은 스택 추적을 포함할 수 있는 백 엔드 오류의 누출을 방지 하는 데 사용할 수 있습니다. 

### <a name="policy-statement"></a>정책 문

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>예제

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 상태 확인-코드 | 루트 요소입니다.                                                                                                | 예      |
| status-code | HTTP 상태 코드에 대해 하나 이상의 요소를 추가 하 여 응답의 상태 코드에 대 한 기본 유효성 검사 작업을 재정의 합니다. | 아니요 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 지정 되지 않음-상태-코드 동작 | API 스키마에 지정 되지 않은 응답의 HTTP 상태 코드에 대해 수행할 [작업](#actions) 입니다.  |  예     | 해당 없음   |
| 오류-변수 이름 | `context.Variables`유효성 검사 오류를 기록할의 변수 이름입니다.  |   예    | 해당 없음   |
| code | 유효성 검사 동작을 재정의할 HTTP 상태 코드입니다. | 예 | 해당 없음 |
| 작업 | API 스키마에 지정 되지 않은 일치 상태 코드에 대해 수행할 [작업](#actions) 입니다. API 스키마에 상태 코드가 지정 된 경우에는이 재정의가 적용 되지 않습니다. | 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** 아웃 바운드, 오류 발생 시

-   **정책 범위:** 모든 범위


## <a name="validation-errors"></a>유효성 검사 오류
다음 표에는 유효성 검사 정책의 가능한 모든 오류가 나열 되어 있습니다. 

* **세부 정보** -오류를 조사 하는 데 사용할 수 있습니다. 공개적으로 공유 되지 않습니다.
* **공용 응답** -클라이언트에 반환 되는 오류입니다. 구현 세부 정보를 누출 하지 않습니다.

| **이름**                             | **형식**                                                        | **유효성 검사 규칙** | **세부 정보**                                                                                                                                       | **공용 응답**                                                                                                                       | **작업**           |
|----|----|---|---|---|----|
| **유효성 검사-콘텐츠** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | 요청의 본문은 {size} 바이트 길이 이며 구성 된 제한인 {maxSize} 바이트를 초과 합니다.                                                       | 요청의 본문은 {size} 바이트 길이 이며 {maxSize} 바이트 제한을 초과 합니다.                                                          | 검색/방지 |
||ResponseBody                                                    | SizeLimit           | 응답의 본문은 {size} 바이트 길이 이며 구성 된 제한인 {maxSize} 바이트를 초과 합니다.                                                      | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody                                                     | Unspecified         | 지정 되지 않은 내용 유형 {messageContentType}은 허용 되지 않습니다.                                                                                     | 지정 되지 않은 내용 유형 {messageContentType}은 허용 되지 않습니다.                                                                             | 검색/방지 |
| {messageContentType}                 | ResponseBody                                                    | Unspecified         | 지정 되지 않은 내용 유형 {messageContentType}은 허용 되지 않습니다.                                                                                     | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| | ApiSchema                                                       |                     | API의 스키마가 없거나 확인 하지 못했습니다.                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
|                                      | ApiSchema                                                       |                     | API의 스키마에서 정의를 지정 하지 않습니다.                                                                                                        | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody/ResponseBody                                      | MissingDefinition   | API의 스키마에 내용 유형 {messageContentType}과 (와) 연결 된 정의 {definitionName}이 (가) 없습니다.                        | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | 요청 본문이 내용 유형 {messageContentType}과 (와) 연결 된 정의 {definitionName}을 (를) 따르지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}                  | 요청 본문이 내용 유형 {messageContentType}과 (와) 연결 된 정의 {definitionName}을 (를) 따르지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}            | 검색/방지 |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | 응답의 본문이 내용 유형 {messageContentType}과 (와) 연결 된 정의 {definitionName}에 맞지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}                                       | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
|                                      | RequestBody                                                     | ValidationException | 콘텐츠 형식 {messageContentType}에 대 한 요청 본문의 유효성을 검사할 수 없습니다.<br/><br/>{예외 정보}                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
|                                      | ResponseBody                                                    | ValidationException | 콘텐츠 형식 {messageContentType}에 대 한 응답 본문의 유효성을 검사할 수 없습니다.<br/><br/>{예외 정보}                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| **유효성 검사-매개 변수/유효성 검사 헤더** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{헤드 Ername}           | QueryParameter/PathParameter/RequestHeader                  | Unspecified         | 지정 되지 않은 경우 {path 매개 변수/쿼리 매개 변수/헤더} {paramName}는 허용 되지 않습니다.                                                               | 지정 되지 않은 경우 {path 매개 변수/쿼리 매개 변수/헤더} {paramName}는 허용 되지 않습니다.                                                       | 검색/방지 |
| HeaderName                         | ResponseHeader                                                  | Unspecified         | 지정 되지 않은 헤더 {헤드 Ername}은 (는) 허용 되지 않습니다.                                                                                                   | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
|                                      |ApiSchema                                                       |                     | API의 스키마가 없거나 확인할 수 없습니다.                                                                                            | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
|                                       | ApiSchema                                                       |                     | API 스키마가 정의를 지정 하지 않습니다.                                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| ParamName                          | QueryParameter/PathParameter/RequestHeader/ResponseHeader | MissingDefinition   | API의 스키마에 {definitionName} 정의가 포함 되어 있지 않습니다 .이는 {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}와 연결 되어 있습니다.  | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| ParamName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | 요청은 {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}에 대해 여러 값을 포함할 수 없습니다.                                           | 요청은 {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}에 대해 여러 값을 포함할 수 없습니다.                                   | 검색/방지 |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | 응답에는 {헤드 Ername} 헤더에 대 한 여러 값을 사용할 수 없습니다.                                                                              | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| ParamName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}의 값이 정의를 따르지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}                                          | {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}의 값이 정의를 따르지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}                              | 검색/방지 |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | {헤드 Ername} 헤더의 값이 정의를 따르지 않습니다.<br/><br/>{valError. 메시지} 줄: {valError. LineNumber}, 위치: {valError. LinePosition}                                                                              | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| ParamName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | 정의에 따라 {query 매개 변수/경로 매개 변수/헤더} {paramName}의 값을 구문 분석할 수 없습니다. <br/><br/>고급. 메시지나                                | 정의에 따라 {query 매개 변수/경로 매개 변수/헤더} {paramName}의 값을 구문 분석할 수 없습니다. <br/><br/>고급. 메시지나                      | 검색/방지 |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | 정의에 따라 {헤드 Ername} 헤더의 값을 구문 분석할 수 없습니다.                                                                  | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| ParamName                          | QueryParameter/PathParameter/RequestHeader                  | ValidationError     | {쿼리 매개 변수/경로 매개 변수/헤더} {paramName}의 유효성을 검사할 수 없습니다.<br/><br/>{예외 정보}                                                                      | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| HeaderName                         | ResponseHeader                                                  | ValidationError     | {헤드 Ername} 헤더의 유효성을 검사할 수 없습니다.<br/><br/>{예외 정보}                                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |
| **상태 확인-코드**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {상태-코드}                        | StatusCode                                                      | Unspecified         | 응답 상태 코드 {status-code}은 (는) 허용 되지 않습니다.                                                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에 게 문의 하세요.                                                       | 검색/방지 |


다음 표에서는 가능한 메시지 값과 함께 유효성 검사 오류의 가능한 모든 이유 값을 보여 줍니다.

|  **이유**         |    **메시지** |
|---|---|  
| 잘못된 요청       |     컨텍스트 변수에 대 한 {Details}, 클라이언트용 {Public response}|
| 응답이 허용 되지 않습니다.  | 컨텍스트 변수에 대 한 {Details}, 클라이언트용 {Public response} |






## <a name="next-steps"></a>다음 단계

정책 사용에 대 한 자세한 내용은 다음을 참조 하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [API 변환](transform-api.md)
-   정책 문 및 해당 설정의 전체 목록에 대 한 [정책 참조](./api-management-policies.md)
-   [정책 샘플](./policy-reference.md)
-   [오류 처리](./api-management-error-handling-policies.md)
