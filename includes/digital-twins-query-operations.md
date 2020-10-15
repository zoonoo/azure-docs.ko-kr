---
author: baanders
description: Azure Digital Twins 쿼리 작업을 위한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078462"
---
## <a name="query-language-features"></a>쿼리 언어 기능

Azure Digital Twins는 쌍 그래프에 대해 광범위 한 쿼리 기능을 제공 합니다. 쿼리는 다양 한 기능을 갖춘 [IoT Hub 쿼리 언어](../articles/iot-hub/iot-hub-devguide-query-language.md) 와 유사한 쿼리 언어에서 SQL과 유사한 구문을 사용 하 여 설명 됩니다.

> [!NOTE]
> 모든 Azure Digital Twins 쿼리 작업은 대/소문자를 구분 합니다.

Azure Digital Twins 쿼리 언어에서 사용할 수 있는 작업은 다음과 같습니다.

디지털 쌍 가져오기 ...
* model ( `IS_OF_MODEL` 연산자 사용)
* 속성 ( [태그 속성](../articles/digital-twins/how-to-use-tags.md)포함)
* interfaces
* relationships
  - 관계의 속성

다음 작업을 사용 하 여 쿼리를 더욱 향상 시킬 수 있습니다.
* 여러 관계 유형 (쿼리)에 대해 쌍을 가져옵니다 `JOIN` . 
  - 미리 보기 중에는 최대 5 개 수준이 `JOIN` 허용 됩니다.
* 상위 쿼리 결과만 선택 ( `Select TOP` 연산자)
* 을 사용 하 여 결과 집합의 항목 수를 계산 합니다. `Select COUNT`
* 프로젝션을 사용 하 여 쿼리에서 반환 되는 열을 선택 합니다.
* 스칼라 함수 `IS_BOOL` (, `IS_DEFINED` ,,,,,,,)를 사용 `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` `ENDSWITH` 합니다.
* 쿼리 비교 연산자 ( `IN` / `NIN` , `=` , `!=` , `<` `>` `<=` `>=` ,,,)를 사용 합니다.
* `AND` `OR` `NOT` `IS_OF_MODEL` , 스칼라 함수 및 비교 연산자의 조합 (,, 연산자)을 사용 합니다.
* 연속 사용: 쿼리 개체가 페이지 크기 (최대 100)를 사용 하 여 인스턴스화됩니다. API에 대 한 후속 호출에 연속 토큰을 제공 하 여 한 번에 한 페이지씩 디지털 쌍을 검색할 수 있습니다.