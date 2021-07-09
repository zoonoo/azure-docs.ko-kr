---
title: Azure Digital Twins 쿼리 언어 참조 - Functions
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 함수에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 80b32907499d3c8588a7a565972f49923abbe5c7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108296476"
---
# <a name="azure-digital-twins-query-language-reference-functions"></a>Azure Digital Twins 쿼리 언어 참조: Functions

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **함수** 에 대한 참조 정보가 포함되어 있습니다.

## <a name="endswith"></a>ENDSWITH

지정된 문자열이 다른 특정 문자열로 끝나는지 여부를 확인하는 데 사용되는 문자열 함수입니다. 

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithSyntax":::

### <a name="arguments"></a>인수

* `<string-to-check>`: 끝을 확인할 문자열
* `<ending-string>`: 확인할 끝을 나타내는 문자열

### <a name="returns"></a>반환

첫 번째 문자열 식이 두 번째 문자열 식으로 끝나지를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 해당 ID가 `-small`로 끝나는 모든 디지털 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithExample":::

## <a name="is_defined"></a>IS_DEFINED

속성이 정의되어 있는지 여부를 확인하는 형식 검사 및 캐스팅 함수입니다.

이는 속성 값이 기본 형식인 경우에만 지원됩니다. 기본 형식에는 문자열, 부울, 숫자 또는 `null`이 포함됩니다. `DateTime`, 개체 형식 및 배열은 지원되지 않습니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedSyntax":::

### <a name="arguments"></a>인수

`<property>`, 정의되었는지 여부를 결정하는 속성입니다. 속성은 기본 형식이어야 합니다.

### <a name="returns"></a>반환

속성이 값을 할당할지를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 *Location* 속성이 정의된 모든 디지털 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedExample":::

## <a name="is_of_model"></a>IS_OF_MODEL

트윈이 특정 모델 형식인지 여부를 확인하는 형식 검사 및 캐스팅 함수입니다. 지정된 모델에서 상속되는 모델을 포함합니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelSyntax":::

### <a name="arguments"></a>인수

필수:
* `<model-ID>`: 확인할 모델 ID입니다.

선택 사항:
* `<twin-collection>`: 둘 이상의 컬렉션이 있을 때(예: `JOIN`이 사용되는 경우) 검색할 트윈 컬렉션을 지정합니다.
* `exact`: 정확히 일치해야 합니다. 이 매개 변수를 설정하지 않으면 결과 집합에는 지정된 모델에서 상속되는 모델이 있는 트윈이 포함됩니다.

### <a name="returns"></a>반환

지정한 트윈이 지정한 모델 형식과 일치하는지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 모델 형식 `dtmi:example:room;1`이 정확히 일치하는 DT 컬렉션에서 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelExample":::

## <a name="is_bool"></a>IS_BOOL

식에 부울 값이 있는지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다.

이 함수는 쿼리 결과를 처리하는 프로그램에 부울 값이 필요하고, 속성이 부울이 아닌 경우를 필터링하려는 경우에 종종 다른 조건자와 결합됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolSyntax":::

### <a name="arguments"></a>인수

`<expression>`, 부울인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 부울인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 부울 `HasTemperature` 속성이 있는 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolExample":::

다음 쿼리는 위의 예제를 빌드하여 부울 `HasTemperature` 속성이 있고, 해당 속성의 값이 `false`가 아닌 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolNotFalseExample":::

## <a name="is_number"></a>IS_NUMBER

식에 숫자 값이 있는지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다.

이 함수는 쿼리 결과를 처리하는 프로그램에 숫자 값이 필요하고, 속성이 숫자가 아닌 경우를 필터링하려는 경우에 종종 다른 조건자와 결합됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberSyntax":::

### <a name="arguments"></a>인수

`<expression>`, 숫자인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 숫자 `Capacity` 속성이 있고 해당 값이 0과 같지 않은 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberExample":::

## <a name="is_string"></a>IS_STRING

식에 문자열 값이 있는지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다. 

이 함수는 쿼리 결과를 처리하는 프로그램에 문자열 값이 필요하고, 속성이 문자열이 아닌 경우를 필터링하려는 경우에 종종 다른 조건자와 결합됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringSyntax":::

### <a name="arguments"></a>인수

`<expression>`, 문자열인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 문자열 속성 `Status` 속성이 있고 해당 값이 `Completed`와 같지 않은 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringExample":::

## <a name="is_null"></a>IS_NULL

식의 값이 `null`인지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullSyntax":::

### <a name="arguments"></a>인수

`<expression>`, Null인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 `null`인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 온도에 대해 Null 값이 없는 트윈을 반환합니다. 이 쿼리에 사용되는 `NOT` 연산자에 대한 자세한 내용은 [Azure Digital Twins 쿼리 언어 참조: 연산자](reference-query-operators.md#logical-operators)를 참조하세요.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullExample":::

## <a name="is_primitive"></a>IS_PRIMITIVE

식의 값이 기본 형식(문자열, 부울, 숫자 또는 `null`)인지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다.

이 함수는 쿼리 결과를 처리하는 프로그램에 기본 형식 값이 필요하고, 속성이 기본 형식이 아닌 경우를 필터링하려는 경우에 종종 다른 조건자와 결합됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveSyntax":::

### <a name="arguments"></a>인수

`<expression>`, 기본 형식인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 `null`) 중 하나인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 `area` 속성이 기본 형식인 경우에만 ID가 'ABC'인 팩터리의 `area` 속성을 반환합니다. 쿼리 결과에서 특정 열을 프로젝팅하는 것에 대한 자세한 내용은(이 쿼리가 `area`에서 수행하는 것처럼) [Azure Digital Twins 쿼리 언어 참조: SELECT 절](reference-query-clause-select.md#select-columns-with-projections)을 참조하세요.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveExample":::

## <a name="is_object"></a>IS_OBJECT

식의 값이 JSON 개체 형식인지 여부를 확인하기 위한 형식 검사 및 캐스팅 함수입니다.

이 함수는 쿼리 결과를 처리하는 프로그램에 JSON 개체가 필요하고, 값이 JSON 개체가 아닌 경우를 필터링하려는 경우에 종종 다른 조건자와 결합됩니다.

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectSyntax":::

### <a name="arguments"></a>인수

`<expression>`, 개체 형식인지 여부를 확인하는 식입니다.

### <a name="returns"></a>반환

지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 이 개체가 `MapObject`이고, 자식 속성 `TemperatureReading`이 없는 모든 디지털 트윈을 선택합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectExample":::

## <a name="startswith"></a>STARTSWITH

지정된 문자열이 다른 특정 문자열로 시작하는지 여부를 확인하는 데 사용되는 문자열 함수입니다. 

### <a name="syntax"></a>구문

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithSyntax":::

### <a name="arguments"></a>인수

* `<string-to-check>`: 시작을 확인할 문자열
* `<beginning-string>`: 확인할 시작을 나타내는 문자열

### <a name="returns"></a>반환

첫 번째 문자열 식이 두 번째 문자열 식으로 시작하는지 여부를 나타내는 부울 값입니다.

### <a name="example"></a>예제

다음 쿼리는 해당 ID가 `area1-`로 시작하는 모든 디지털 트윈을 반환합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithExample":::
