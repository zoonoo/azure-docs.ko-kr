---
title: 쿼리 언어
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어의 기본 사항을 이해합니다.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490979"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 쿼리 언어 정보

Azure Digital Twins의 중심은 디지털 트윈과 관계로 생성된 [트윈 그래프](concepts-twins-graph.md)입니다. 

이 그래프를 쿼리하여 포함된 디지털 트윈과 관계에 대한 정보를 확인할 수 있습니다. 이러한 쿼리는 **Azure Digital Twins 쿼리 언어** 라고도 하는 SQL 같은 사용자 지정 쿼리 언어로 작성합니다. 이는 많은 유사한 기능을 갖춘 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md)와 비슷합니다.

이 문서에서는 쿼리 언어 및 해당 기능에 대한 기본 사항을 설명합니다. 쿼리 구문에 대한 자세한 예와 쿼리 요청을 실행하는 방법은 [*방법: 트윈 그래프 쿼리*](how-to-query-graph.md)를 참조하세요.

## <a name="about-the-queries"></a>쿼리 정보

Azure Digital Twins 쿼리 언어를 사용하여 다음에 따라 디지털 트윈을 검색할 수 있습니다.
* 속성([태그 속성](how-to-use-tags.md) 포함)
* 모델
* relationships
  - 관계의 속성

클라이언트 앱에서 서비스로 쿼리를 제출하려면 Azure Digital Twins [**Query API**](/rest/api/digital-twins/dataplane/query)를 사용합니다. API를 사용하는 한 가지 방법은 Azure Digital Twins용 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) 중 하나를 사용하는 것입니다.

### <a name="considerations-for-querying"></a>쿼리 고려 사항

Azure Digital Twins에 대한 쿼리를 작성하는 경우 다음 고려 사항을 염두에 두어야 합니다.
* **대/소문자 구분**: 모든 Azure Digital twins 쿼리 작업은 대/소문자를 구분하므로 모델에 정의된 정확한 이름을 사용해야 합니다. 속성 이름의 철자가 틀렸거나 대/소문자가 잘못된 경우에는 결과 집합이 비어 있고 오류가 반환되지 않습니다.
* **이스케이프 작은따옴표**: 쿼리 텍스트의 데이터에 작은따옴표 문자가 포함된 경우 따옴표를 `\` 문자로 이스케이프해야 합니다. *D'Souza* 의 속성 값을 처리하는 예는 다음과 같습니다.

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>참조: 식 및 조건

이 섹션에서는 Azure Digital Twins 쿼리를 작성하는 데 사용할 수 있는 연산자 및 함수에 대해 설명합니다. 이러한 기능을 사용하는 방법을 보여주는 쿼리 예는 [*방법: 트윈 그래프 쿼리*](how-to-query-graph.md)를 참조하세요.

### <a name="operators"></a>연산자

다음과 같은 연산자가 지원됩니다.

| 패밀리 | 연산자 |
| --- | --- |
| 논리 |`AND`, `OR`, `NOT` |
| 비교 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| 포함 | `IN`, `NIN` |

### <a name="functions"></a>Functions

지원되는 형식 검사 및 캐스팅 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| `IS_DEFINED` | 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다. 이는 값이 기본 형식인 경우에만 지원됩니다. 기본 형식에는 문자열, 부울, 숫자 또는 `null`이 포함됩니다. `DateTime`, 개체 형식 및 배열은 지원되지 않습니다. |
| `IS_OF_MODEL` | 지정한 트윈이 지정한 모델 형식과 일치하는지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_BOOL` | 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_NUMBER` | 지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_STRING` | 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_NULL` | 지정한 식의 형식이 널인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_PRIMITIVE` | 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 `null`)인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_OBJECT` | 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다. |

지원되는 문자열 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다. |
| `ENDSWITH(x, y)` | 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다. |

## <a name="query-limitations"></a>쿼리 제한 사항

이 섹션에서는 쿼리 언어의 제한 사항에 대해 설명합니다.

* 타이밍: 인스턴스의 변경 내용이 쿼리에 반영될 때까지 최대 10초가 지연될 수 있습니다. 예를 들어 DigitalTwins API를 사용하여 트윈 생성 또는 삭제와 같은 작업을 완료하는 경우 Query API 요청에 결과가 즉시 반영되지 않을 수 있습니다. 잠시 기다리면 해결할 수 있습니다.
* `FROM` 문 내에서는 하위 쿼리가 지원되지 않습니다.
* `OUTER JOIN` 의미 체계가 지원되지 않습니다. 즉, 관계의 순위가 0이면 전체 "row"가 출력 결과 집합에서 제거됩니다.
* 그래프 순회 깊이는 쿼리당 5개의 `JOIN` 수준으로 제한됩니다.
* Azure Digital Twins의 관계는 독립 엔터티로 쿼리할 수 없습니다. 또한 관계의 원본 트윈에 대한 정보를 제공해야 합니다. 즉, 쿼리가 시작되는 트윈을 쿼리가 선언하도록 관계를 쿼리하는 데 사용되는 `JOIN` 작업에 몇 가지 제한 사항이 있습니다. 이에 대한 예는 *방법: 트윈 그래프 쿼리* 문서에서 [*관계별 쿼리*](how-to-query-graph.md#query-by-relationship)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[*방법: 트윈 그래프 쿼리*](how-to-query-graph.md)에서 쿼리를 작성하는 방법을 알아보고 클라이언트 코드 예를 확인하세요.