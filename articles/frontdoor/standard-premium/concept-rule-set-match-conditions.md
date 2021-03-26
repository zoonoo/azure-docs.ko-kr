---
title: Azure Front 도어 표준/프리미엄 규칙 집합 일치 조건 구성
description: 이 문서에서는 Azure Front 도어 표준/프리미엄 규칙 집합에서 사용할 수 있는 다양 한 일치 조건 목록을 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561516"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front 도어 표준/프리미엄 (미리 보기) 규칙 집합 일치 조건

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure 전면 도어 표준/프리미엄 [규칙 집합](concept-rule-set.md)에서 규칙은 0 개 이상의 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure Front 도어 표준/프리미엄 규칙 집합에서 사용할 수 있는 일치 조건에 대 한 자세한 설명을 제공 합니다.

규칙의 첫 번째 파트는 일치 조건 또는 일치 조건 세트입니다. 최대 10개의 일치 조건으로 규칙을 구성할 수 있습니다. 일치 조건은 정의된 작업이 수행되는 특정 유형의 요청을 식별합니다. 여러 일치 조건을 사용하면 AND 논리를 사용하여 일치 조건이 그룹화됩니다. 여러 값을 지 원하는 모든 일치 조건에 대해 또는 논리가 사용 됩니다.

일치 조건을 사용 하 여 다음을 수행할 수 있습니다.

* 특정 IP 주소, 국가 또는 지역을 기준으로 요청을 필터링합니다.
* 헤더 정보에 의한 필터 요청
* 모바일 디바이스 또는 데스크톱 디바이스에서 요청을 필터링합니다.
* 요청 파일 이름 및 파일 확장명에서 요청을 필터링 합니다.
* 요청 URL, 프로토콜, 경로, 쿼리 문자열, 사후 인수 등의 요청을 필터링 합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="device-type"></a><a name="IsDevice"></a> 장치 유형

**장치 유형** 일치 조건을 사용 하 여 모바일 장치 또는 데스크톱 장치에서 수행 된 요청을 식별 합니다.  

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 연산자 | <ul><li>Azure Portal에서 다음을 수행 합니다. `Equal``Not Equal`</li><li>ARM 템플릿: `Equal` ; `negateCondition` 속성을 사용 하 여 _같지 않음_ 을 지정 합니다. |
| 값 | `Mobile`, `Desktop` |

### <a name="example"></a>예제

이 예제에서는 모바일 장치에서 발생 하는 것으로 검색 된 모든 요청을 일치 시킵니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="장치 유형 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> 사후 인수

Post **args** match 조건을 사용 하 여 post 요청의 본문 내에서 제공 된 인수를 기반으로 요청을 식별 합니다. 단일 일치 조건은 POST 요청의 본문에서 단일 인수와 일치 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!NOTE]
> **사후 인수** 일치 조건은 내용 유형과 함께 작동 합니다 `application/x-www-form-urlencoded` .

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 사후 인수 | POST 인수의 이름을 나타내는 문자열 값입니다. |
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 POST 인수의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 `customerName` 요청 본문에서 인수가 제공 되는 모든 POST 요청을 일치 시키고,의 값은 `customerName` 또는 문자로 시작 합니다 `J` `K` . Case transform을 사용 하 여 입력 값을 대문자로 변환 하 여,, 및로 시작 하는 값이 `J` `j` `K` `k` 모두 일치 하도록 합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="사후 args 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> 쿼리 문자열

**쿼리 문자열** 일치 조건을 사용 하 여 특정 쿼리 문자열이 포함 된 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!NOTE]
> 전체 쿼리 문자열은 선행 없이 단일 문자열로 일치 됩니다 `?` .

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 쿼리 문자열 | 일치 시킬 쿼리 문자열의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. `?`쿼리 문자열의 시작 부분에를 포함 하지 않습니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예에서는 쿼리 문자열에 문자열이 포함 된 모든 요청을 일치 시킵니다 `language=en-US` . 일치 조건에서 대/소문자를 구분 하려고 하므로 사례를 변환 하지 않습니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="쿼리 문자열 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> 원격 주소

**원격 주소** 일치 조건은 요청자의 위치 또는 IP 주소에 따라 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

* IP 주소 블록을 지정할 때 CIDR 표기법을 사용 합니다. 즉, IP 주소 블록에 대 한 구문은 기본 IP 주소 뒤에 슬래시와 접두사 크기가 나옵니다. 예를 들어:
    * **IPv4 예**: `5.5.5.64/26` 요청과 일치를 통해 5.5.5.64 주소에서 도착 하는 모든 요청과 일치 합니다.
    * **IPv6 예제**: `1:2:3:/48` 1:2:3:0:0:0:0:0 ~ 1:2:3: ffff: ffff: ffff: ffff: ffff 주소에서 도착 하는 모든 요청과 일치 합니다.
* 여러 IP 주소와 IP 주소 블록을 지정 하면 ' OR ' 논리가 적용 됩니다.
    * **IPv4 예**: 두 개의 IP 주소를 추가 하는 경우 `1.2.3.4` `10.20.30.40` 주소 1.2.3.4 또는 10.20.30.40에서 도착 하는 모든 요청에 대해 조건이 일치 됩니다.
    * **IPv6 예**: 두 개의 IP 주소를 추가 하는 경우 `1:2:3:4:5:6:7:8` `10:20:30:40:50:60:70:80` 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80 주소에서 도착 하는 모든 요청에 대해 조건이 일치 됩니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal에서,, `Geo Match` `Geo Not Match` 또는입니다 `IP Match` . `IP Not Match`</li><li>ARM 템플릿: `GeoMatch` ,, `IPMatch` 속성을 사용 `negateCondition` 하 여 _지역 불일치_ 또는 IP가 _일치 하지 않습니다_ .</li></ul> |
| 값 | <ul><li>`IP Match`또는 `IP Not Match` 연산자: 하나 이상의 IP 주소 범위를 지정 합니다. 여러 IP 주소 범위를 지정 하는 경우 또는 논리를 사용 하 여 평가 됩니다.</li><li>`Geo Match`또는 `Geo Not Match` 연산자: 국가 코드를 사용 하 여 하나 이상의 위치를 지정 합니다.</li></ul> |

### <a name="example"></a>예제

이 예제에서는 요청이 미국에서 시작 되지 않은 모든 요청과 일치 합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="원격 주소 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> 요청 본문

요청 **본문** 일치 조건은 요청의 본문에 표시 되는 특정 텍스트를 기반으로 하는 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!NOTE]
> 요청 본문의 크기가 64KB를 초과 하는 경우 **요청 본문** 일치 조건에 대해 처음 64kb만 고려 됩니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 본문 텍스트의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 본문에 문자열이 포함 된 모든 요청을 일치 시킵니다 `ERROR` . 일치를 평가 하기 전에 요청 본문을 대문자로 변환 `error` 하 고, 다른 대/소문자 변형도이 일치 조건을 트리거합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="요청 본문 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> 요청 파일 이름

요청 **파일 이름** 일치 조건은 요청 URL에 지정 된 파일 이름을 포함 하는 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 파일 이름의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 이름이 인 모든 요청을 일치 시킵니다 `media.mp4` . 일치 항목을 확인 하기 전에 파일 이름을 소문자로 변환 `MEDIA.MP4` 하 고, 그 외의 경우에도이 일치 조건을 트리거합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="요청 파일 이름 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> 요청 파일 확장명

**요청 파일 확장명** 일치 조건은 요청 URL의 파일 이름에 지정 된 파일 확장명을 포함 하는 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!NOTE]
> 선행 기간을 포함 하지 않습니다. 예를 들어 `.html` 대신 `html`을 사용합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 파일 확장명의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 선행 기간을 포함 하지 않습니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 확장명이 또는 인 모든 요청을 일치 시킵니다 `pdf` `docx` . 일치를 평가 하기 전에 요청 파일 확장을 소문자로 변환 하 `PDF` 고,, `DocX` 및 기타 대/소문자 변형을 통해이 일치 조건을 트리거합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="요청 파일 확장 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> 요청 헤더

요청 **헤더** 일치 조건은 요청에 특정 헤더를 포함 하는 요청을 식별 합니다. 이 일치 조건을 사용 하 여 헤더에 해당 값이 있는지 확인 하거나 헤더가 지정 된 값과 일치 하는지 확인할 수 있습니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 헤더 이름 | POST 인수의 이름을 나타내는 문자열 값입니다. |
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 헤더의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 해당 값에 관계 없이 요청이 라는 헤더를 포함 하는 모든 요청을 일치 시킵니다 `MyCustomHeader` .

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="요청 헤더 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Request 메서드

**요청 메서드** 일치 조건은 지정 된 HTTP 요청 메서드를 사용 하는 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal에서 다음을 수행 합니다. `Equal``Not Equal`</li><li>ARM 템플릿: `Equal` ; `negateCondition` 속성을 사용 하 여 _같지 않음_ 을 지정 합니다. |
| 요청 메서드 | `GET`,,,,,,의 하나 이상의 HTTP 메서드입니다 `POST` `PUT` `DELETE` `HEAD` `OPTIONS` `TRACE` . 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |

### <a name="example"></a>예제

이 예제에서는 요청에서 메서드를 사용 하는 모든 요청과 일치 `DELETE` 합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="요청 메서드 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> 요청 경로

요청 **경로** 일치 조건은 요청 URL에 지정 된 경로를 포함 하는 요청을 식별 합니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!NOTE]
> 경로는 호스트 이름 및 슬래시 뒤에 있는 URL의 일부입니다. 예를 들어 URL에서 `https://www.contoso.com/files/secure/file1.pdf` 경로는 `files/secure/file1.pdf` 입니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 경로의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 선행 슬래시를 포함 하지 않습니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 경로가로 시작 하는 모든 요청을 일치 시킵니다 `files/secure/` . 일치 항목을 확인 하기 전에 요청 파일 확장을 소문자로 변환 하 여 `files/SECURE/` 및 기타 대/소문자 변형으로 인 한 요청은이 일치 조건을 트리거합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="요청 경로 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> 요청 프로토콜

**요청 프로토콜** 일치 조건은 지정 된 프로토콜 (HTTP 또는 HTTPS)을 사용 하는 요청을 식별 합니다.

> [!NOTE]
> *프로토콜* 은 종종 *구성표* 라고도 합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal에서 다음을 수행 합니다. `Equal``Not Equal`</li><li>ARM 템플릿: `Equal` ; `negateCondition` 속성을 사용 하 여 _같지 않음_ 을 지정 합니다. |
| 요청 메서드 | `HTTP`, `HTTPS` |

### <a name="example"></a>예제

이 예제에서는 요청에서 프로토콜을 사용 하는 모든 요청을 일치 시킵니다 `HTTP` .

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="요청 프로토콜 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> 요청 URL

지정된 URL과 일치하는 요청을 식별합니다. 전체 URL이 평가 됩니다. 와 일치 하는 여러 값을 지정 하 여 또는 논리를 사용 하 여 결합할 수 있습니다.

> [!TIP]
> 이 규칙 조건을 사용 하는 경우 프로토콜을 포함 해야 합니다. 예를 들어, `https://www.contoso.com` 대신을 사용 `www.contoso.com` 합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list)모든 연산자 |
| 값 | 일치 시킬 요청 URL의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값이 지정 된 경우 또는 논리를 사용 하 여 계산 됩니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 URL이로 시작 하는 모든 요청을 일치 시킵니다 `https://api.contoso.com/customers/123` . 일치 항목을 확인 하기 전에 요청 파일 확장을 소문자로 변환 하 여 `https://api.contoso.com/Customers/123` 및 기타 대/소문자 변형으로 인 한 요청은이 일치 조건을 트리거합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="요청 URL 일치 조건을 보여 주는 포털 스크린샷.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> 운영자 목록

표준 연산자 목록의 값을 허용하는 규칙에는 다음 연산자가 유효합니다.

| 연산자                   | Description                                                                                                                    | ARM 템플릿 지원                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 모두                        | 값이 무엇이 든 관계 없이 일치 하는 항목을 찾습니다.                                                                     | `operator`: `Any`                                               |
| 같음                      | 값이 지정 된 문자열과 정확 하 게 일치 하는 경우 일치 합니다.                                                                   | `operator`: `Equal`                                             |
| 포함                   | 값이 지정 된 문자열을 포함 하는 경우와 일치 합니다.                                                                          | `operator`: `Contains`                                          |
| 미만                  | 값의 길이가 지정 된 정수 보다 작은 경우와 일치 합니다.                                                       | `operator`: `LessThan`                                          |
| 다음보다 큼               | 값의 길이가 지정 된 정수 보다 큰 경우와 일치 합니다.                                                    | `operator`: `GreaterThan`                                       |
| 작거나 같음         | 값의 길이가 지정 된 정수 보다 작거나 같으면 일치 합니다.                                           | `operator`: `LessThanOrEqual`                                   |
| 크거나 같음      | 값의 길이가 지정 된 정수 보다 크거나 같으면 일치 합니다.                                        | `operator`: `GreaterThanOrEqual`                                |
| 시작 문자                | 값이 지정 된 문자열로 시작 하는 경우와 일치 합니다.                                                                       | `operator`: `BeginsWith`                                        |
| 끝 문자                  | 값이 지정 된 문자열로 끝나는 경우 일치 합니다.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | 지정 된 정규식과 일치 하는 값을 찾습니다. [자세한 내용은 아래를 참조 하세요.](#regular-expressions)        | `operator`: `RegEx`                                             |
| 모두 아님                    | 값이 없는 경우와 일치 합니다.                                                                                                | `operator`: `Any` 및 `negateCondition` : `true`                |
| 같지 않음                  | 값이 지정 된 문자열과 일치 하지 않는 경우 일치 합니다.                                                                    | `operator`: `Equal` 및 `negateCondition` : `true`              |
| 포함하지 않음               | 값에 지정 된 문자열이 없으면를 찾습니다.                                                                  | `operator`: `Contains` 및 `negateCondition` : `true`           |
| 보다 작지 않음              | 값의 길이가 지정 된 정수 보다 작지 않을 때 일치 합니다.                                                   | `operator`: `LessThan` 및 `negateCondition` : `true`           |
| 보다 크지 않음           | 값의 길이가 지정 된 정수 보다 크지 않은 경우 일치 합니다.                                                | `operator`: `GreaterThan` 및 `negateCondition` : `true`        |
| 작거나 같음     | 값의 길이가 지정 된 정수 보다 작거나 같으면 일치 합니다.                                       | `operator`: `LessThanOrEqual` 및 `negateCondition` : `true`    |
| 작거나 같음 | 값의 길이가 지정 된 정수 보다 작거나 같으면 일치 합니다.                                    | `operator`: `GreaterThanOrEqual` 및 `negateCondition` : `true` |
| 다음으로 시작 안 함            | 값이 지정 된 문자열로 시작 하지 않는 경우와 일치 합니다.                                                               | `operator`: `BeginsWith` 및 `negateCondition` : `true`         |
| 다음으로 끝나지 않음              | 값이 지정 된 문자열로 끝나지 않는 경우 일치 합니다.                                                                 | `operator`: `EndsWith` 및 `negateCondition` : `true`           |
| RegEx 아님                  | 값이 지정 된 정규식과 일치 하지 않는 경우와 일치 합니다. [자세한 내용은 아래를 참조 하세요.](#regular-expressions) | `operator`: `RegEx` 및 `negateCondition` : `true`              |

> [!TIP]
> *작거나 같음* 이나 *크거나 같음* 처럼 숫자 연산자의 경우 길이를 기준으로 비교합니다. 일치 조건의 값은 비교할 길이를 지정 하는 정수 여야 합니다.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> 정규식

정규식은 다음 작업을 지원 하지 않습니다.

* 하위 식 역참조 및 캡처.
* 임의의 너비가 0 인 어설션.
* 서브루틴 참조 및 재귀 패턴.
* 조건부 패턴.
* 컨트롤 동사를 역 추적 합니다.
* `\C`단일 바이트 지시문입니다.
* `\R`줄 바꿈 일치 지시문입니다.
* `\K`Match reset 지시문의 시작입니다.
* 설명선과 포함 된 코드입니다.
* 원자성 그룹화 및 possessive 수량자

## <a name="arm-template-support"></a>ARM 템플릿 지원

Azure Resource Manager 템플릿을 사용 하 여 규칙 집합을 구성할 수 있습니다. [예제 템플릿을 참조](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set)하세요. 위의 예제에 포함 된 JSON 또는 Bicep 코드 조각을 사용 하 여 일치 조건을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [규칙 집합](concept-rule-set.md)에 대해 자세히 알아보세요.
* [첫 번째 규칙 집합을 구성](how-to-configure-rule-set.md)하는 방법에 대해 알아봅니다.
* [규칙 집합 작업](concept-rule-set-actions.md)에 대해 자세히 알아보세요.
