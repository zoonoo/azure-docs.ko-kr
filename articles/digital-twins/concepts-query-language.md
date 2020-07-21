---
title: 쿼리 언어
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 저장소 언어의 기본 사항을 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3ed5d6605097b31dfaa58a2d37e71d3a6702bee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537501"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins의 쿼리 언어 정보

Azure digital 쌍의 중심은 **디지털** 쌍 및 **관계**에서 생성 된 쌍 [**그래프**](concepts-twins-graph.md)입니다. 이 그래프를 쿼리하여 디지털 쌍 및 여기에 포함 된 관계에 대 한 정보를 가져올 수 있습니다. 이러한 쿼리는 **Azure Digital Twins 쿼리 저장소 언어**라는 사용자 지정 SQL 유사 쿼리 언어로 작성 됩니다.

클라이언트 앱에서 서비스로 쿼리를 제출 하려면 Azure Digital Twins [**쿼리 API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)를 사용 합니다. 이를 통해 개발자는 쿼리를 작성 하 고 필터를 적용 하 여 쌍 그래프에서 디지털 쌍 집합을 찾을 수 있으며, Azure digital 쌍 시나리오에 대 한 기타 정보를 찾을 수 있습니다.

## <a name="query-language-features"></a>쿼리 언어 기능

Azure Digital Twins는 쌍 그래프에 대해 광범위 한 쿼리 기능을 제공 합니다. 쿼리는 다양 한 기능을 갖춘 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md) 와 유사한 쿼리 언어에서 SQL과 유사한 구문을 사용 하 여 설명 됩니다.

> [!NOTE]
> 모든 Azure Digital Twins 쿼리 작업은 대/소문자를 구분 합니다.

Azure Digital Twins 쿼리 저장소 언어에서 사용할 수 있는 작업은 다음과 같습니다.
* 디지털 쌍의 속성을 통해 쌍를 가져옵니다.
* 디지털 쌍 인터페이스를 통해 쌍을 가져옵니다.
* 관계 속성으로 쌍를 가져옵니다.
* 여러 관계 유형 (쿼리)에 대해 쌍을 가져옵니다 `JOIN` . 허용 되는 수에는 제한 사항이 있습니다 `JOIN` (공개 미리 보기의 경우 한 수준).
* 쌍 `IS_OF_MODEL(twinCollection, twinTypeName)` 의 [모델](concepts-models.md)을 기준으로 필터링 할 수 있는 사용자 지정 함수를 사용 합니다. 상속을 지원 합니다.
* 스칼라 함수 `IS_BOOL` (, `IS_DEFINED` ,,,,,,,)를 사용 `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` `ENDS_WITH` 합니다.
* 쿼리 비교 연산자 ( `IN` / `NIN` , `=` , `!=` , `<` `>` `<=` `>=` ,,,)를 사용 합니다.
* 위의 모든 조합 ( `AND` , `OR` , `NOT` 연산자)을 사용 합니다.
* 연속 사용: 쿼리 개체가 페이지 크기 (최대 100)를 사용 하 여 인스턴스화됩니다. API에 대 한 후속 호출에 연속 토큰을 제공 하 여 한 번에 한 페이지씩 디지털 쌍을 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

쿼리를 작성 하 고 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)에서 클라이언트 코드 예제를 확인 하는 방법에 대해 알아봅니다.
