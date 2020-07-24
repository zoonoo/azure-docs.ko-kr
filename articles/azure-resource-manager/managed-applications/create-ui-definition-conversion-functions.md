---
title: UI 정의 변환 함수 만들기
description: 데이터 형식과 인코딩 간에 값을 변환할 때 사용 하는 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098196"
---
# <a name="createuidefinition-conversion-functions"></a>CreateUiDefinition 변환 함수

이러한 함수를 사용하여 JSON 데이터 형식 및 인코딩 간에 값을 변환할 수 있습니다.

## <a name="bool"></a>bool

매개 변수를 부울로 변환합니다. 이 함수는 숫자, 문자열 및 부울 형식의 매개 변수를 지원합니다. JavaScript의 부울과 유사 하 게 또는를 제외한 모든 값은를 `0` `'false'` 반환 `true` 합니다.

다음 예제는 `true`을 반환합니다.

```json
"[bool(1)]"
```

다음 예제는 `false`을 반환합니다.

```json
"[bool(0)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[bool(true)]"
```

다음 예제는 `true`을 반환합니다.

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

매개 변수를 base-64로 인코드된 문자열에서 디코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"Contoso"`을 반환합니다.

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

매개 변수를 URL로 인코드된 문자열에서 디코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"https://portal.azure.com/"`을 반환합니다.

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

매개 변수를 base-64로 인코드된 문자열로 인코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"Q29udG9zbw=="`을 반환합니다.

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

매개 변수를 URL로 인코드된 문자열로 인코드합니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `"https%3A%2F%2Fportal.azure.com%2F"`을 반환합니다.

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

매개 변수를 부동 소수점으로 변환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수를 지원합니다.

다음 예제는 `1.0`을 반환합니다.

```json
"[float('1.0')]"
```

다음 예제는 `2.9`을 반환합니다.

```json
"[float(2.9)]"
```

## <a name="int"></a>int

매개 변수를 정수로 변환합니다. 이 함수는 숫자 및 문자열 형식의 매개 변수를 지원합니다.

다음 예제는 `1`을 반환합니다.

```json
"[int('1')]"
```

다음 예제는 `2`을 반환합니다.

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

매개 변수를 네이티브 형식으로 변환합니다. 즉, 이 함수는 `string()`의 역함수입니다. 이 함수는 문자열 형식의 매개 변수만 지원합니다.

다음 예제는 `1`을 반환합니다.

```json
"[parse('1')]"
```

다음 예제는 `true`을 반환합니다.

```json
"[parse('true')]"
```

다음 예제는 `[1,2,3]`을 반환합니다.

```json
"[parse('[1,2,3]')]"
```

다음 예제는 `{"type":"webapp"}`을 반환합니다.

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>문자열

매개 변수를 문자열로 변환합니다. 이 함수는 모든 JSON 데이터 형식의 매개 변수를 지원합니다.

다음 예제는 `"1"`을 반환합니다.

```json
"[string(1)]"
```

다음 예제는 `"2.9"`을 반환합니다.

```json
"[string(2.9)]"
```

다음 예제는 `"[1,2,3]"`을 반환합니다.

```json
"[string([1,2,3])]"
```

다음 예제는 `"{"type":"webapp"}"`을 반환합니다.

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.
