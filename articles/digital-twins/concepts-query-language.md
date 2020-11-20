---
title: 쿼리 언어
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어의 기본 사항을 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 89e95b0c56ce5603096fb1ac9af74cb0ad53ee6b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955228"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins의 쿼리 언어 정보

Azure digital 쌍의 중심은 디지털 쌍 및 관계에서 생성 된 쌍 [그래프](concepts-twins-graph.md)입니다. 

이 그래프를 쿼리하여 디지털 쌍 및 여기에 포함 된 관계에 대 한 정보를 가져올 수 있습니다. 이러한 쿼리는 **Azure Digital Twins 쿼리 언어** 라고 하는 사용자 지정 SQL 유사 쿼리 언어로 작성 됩니다. 이는 다양 한 기능을 갖춘 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md) 와 비슷합니다.

이 문서에서는 쿼리 언어 및 해당 기능에 대 한 기본 사항을 설명 합니다. 쿼리 구문에 대 한 자세한 예제와 쿼리 요청을 실행 하는 방법에 대 한 자세한 내용은 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

## <a name="about-the-queries"></a>쿼리 정보

Azure 디지털 쌍 쿼리 언어를 사용 하 여 ...에 따라 디지털 쌍을 검색할 수 있습니다.
* 속성 ( [태그 속성](how-to-use-tags.md)포함)
* 모델
* relationships
  - 관계의 속성

클라이언트 앱에서 서비스로 쿼리를 제출 하려면 Azure Digital Twins [**쿼리 API**](/rest/api/digital-twins/dataplane/query)를 사용 합니다. API를 사용 하는 한 가지 방법은 Azure Digital Twins 용 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 중 하나를 사용 하는 것입니다.

## <a name="reference-expressions-and-conditions"></a>참조: 식 및 조건

이 섹션에서는 Azure Digital Twins 쿼리를 작성 하는 데 사용할 수 있는 연산자 및 함수에 대해 설명 합니다. 이러한 기능을 사용 하는 방법을 보여 주는 예제 쿼리 [*는 방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

> [!NOTE]
> 모든 Azure Digital Twins 쿼리 작업은 대/소문자를 구분 하므로 모델에 정의 된 정확한 이름을 사용 해야 합니다. 속성 이름의 철자가 틀렸거나 잘못 된 경우에는 오류가 반환 되지 않고 결과 집합이 비어 있습니다.

### <a name="operators"></a>연산자

다음과 같은 연산자가 지원됩니다.

| 패밀리 | 연산자 |
| --- | --- |
| 논리 |`AND`, `OR`, `NOT` |
| 비교 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| 포함 | `IN`, `NIN` |

### <a name="functions"></a>Functions

지원 되는 형식 검사 및 캐스팅 함수는 다음과 같습니다.

| 함수 | Description |
| -------- | ----------- |
| `IS_DEFINED` | 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다. 이는 값이 기본 형식인 경우에만 지원 됩니다. 기본 형식에는 문자열, 부울, 숫자 또는가 포함 됩니다 `null` . `DateTime`, 개체 형식 및 배열은 지원 되지 않습니다. |
| `IS_OF_MODEL` | 지정 된 쌍이 지정 된 모델 형식과 일치 하는지 여부를 나타내는 부울 값을 반환 합니다. |
| `IS_BOOL` | 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_NUMBER` | 지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_STRING` | 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_NULL` | 지정한 식의 형식이 널인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_PRIMITIVE` | 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 `null`)인지 여부를 나타내는 부울 값을 반환합니다. |
| `IS_OBJECT` | 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다. |

지원 되는 문자열 함수는 다음과 같습니다.

| 함수 | Description |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다. |
| `ENDSWITH(x, y)` | 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다. |

## <a name="query-limitations"></a>쿼리 제한 사항

이 섹션에서는 쿼리 언어의 제한 사항에 대해 설명 합니다.

* 타이밍: 인스턴스의 변경 내용이 쿼리에 반영 될 때까지 최대 10 초가 지연 될 수 있습니다. 예를 들어 DigitalTwins API를 사용 하 여 쌍 생성 또는 삭제와 같은 작업을 완료 하는 경우 쿼리 API 요청에 결과가 즉시 반영 되지 않을 수 있습니다. 짧은 기간 동안에는 문제를 해결 하기에 충분 해야 합니다.
* 문 내에서 하위 쿼리를 지원 하지 않습니다 `FROM` .
* `OUTER JOIN` 의미 체계가 지원 되지 않습니다. 즉, 관계의 순위가 0 이면 전체 "row"가 출력 결과 집합에서 제거 됩니다.
* 그래프 순회 깊이는 쿼리당 5 개 수준으로 제한 됩니다 `JOIN` .
* 작업의 소스가 `JOIN` 제한 되어 있습니다. 쿼리는 쿼리가 시작 되는 위치를 선언 해야 합니다.

## <a name="next-steps"></a>다음 단계

쿼리를 작성 하 고 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)에서 클라이언트 코드 예제를 확인 하는 방법에 대해 알아봅니다.