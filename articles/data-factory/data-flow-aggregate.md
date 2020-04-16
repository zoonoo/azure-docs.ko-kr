---
title: 매핑 데이터 흐름의 집계 변환
description: 매핑 데이터 흐름 집계 변환을 사용하여 Azure Data Factory에서 규모에 따라 데이터를 집계하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 050fcc33f0e3d65ca8e94b3be143c5b1ae77608d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416548"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 집계 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

집계 변환은 데이터 스트림의 열 집계를 정의합니다. 표현식 작성기에서는 기존 또는 계산된 열별로 그룹화된 SUM, MIN, MAX 및 COUNT와 같은 다양한 유형의 집계를 정의할 수 있습니다.

## <a name="group-by"></a>Group By

기존 열을 선택하거나 집계에 대한 절별로 그룹으로 사용할 새 계산된 열을 만듭니다. 기존 열을 사용하려면 드롭다운에서 열을 선택합니다. 계산된 새 열을 만들려면 절 위로 마우스를 가져가고 **계산된 열을 클릭합니다.** 그러면 [데이터 흐름 식 빌더가](concepts-data-flow-expression-builder.md)열립니다. 계산된 열을 만든 후 Name 아래에 출력 열 이름을 **필드로** 입력합니다. 절별로 그룹을 추가하려면 기존 절 위로 마우스를 가져가고 더하기 아이콘을 클릭합니다.

![설정별로 변환 그룹 집계](media/data-flow/agg.png "설정별로 변환 그룹 집계")

절별 그룹은 집계 변환에서 선택 사항입니다.

## <a name="aggregate-column"></a>집계 열 

**집계** 탭으로 이동하여 집계 식을 작성합니다. 집계를 사용하여 기존 열을 덮어쓰거나 새 이름으로 새 필드를 만들 수 있습니다. 집계 식은 열 이름 선택기 옆의 오른쪽 상자에 입력됩니다. 식을 편집하려면 텍스트 상자를 클릭하여 표현식 작성기를 엽니다. 추가 집계를 추가하려면 기존 표현식 위로 마우스를 가져가고 더하기 아이콘을 클릭하여 새 집계 열 또는 [열 패턴을](concepts-data-flow-column-pattern.md)만듭니다.

각 집계 식에는 하나 이상의 집계 함수가 포함되어야 합니다.

![집계 변환 집계 설정](media/data-flow/agg2.png "집계 변환 집계 설정")


> [!NOTE]
> 디버그 모드에서식 빌더는 집계 함수가 있는 데이터 미리 보기를 생성할 수 없습니다. 집계 변환에 대한 데이터 미리 보기를 보려면 식 작성기를 닫고 '데이터 미리 보기' 탭을 통해 데이터를 봅니다.

## <a name="reconnect-rows-and-columns"></a>행과 열 다시 연결

집계 변환은 SQL 집계 선택 쿼리와 유사합니다. 절 또는 집계 함수별로 그룹에 포함되지 않은 열은 집계 변환의 출력으로 흐르지 않습니다. 집계된 출력에 다른 열을 포함하려면 다음 방법 중 하나를 수행하십시오.

* 추가 열을 포함하거나 `last()` `first()` 포함하려면 집계 함수를 사용합니다.
* [자체 조인 패턴을](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)사용하여 열을 출력 스트림에 다시 조인합니다.

## <a name="removing-duplicate-rows"></a>중복 행 제거

집계 변환의 일반적인 용도는 원본 데이터에서 중복 항목을 제거하거나 식별하는 것입니다. 이 프로세스를 중복 제거라고 합니다. 키별 그룹 집합에 따라 선택한 추론을 사용하여 유지할 중복 행을 결정합니다. 일반적인 `first()`추론은 `last()`" 및 `max()` `min()`. [열 패턴을](concepts-data-flow-column-pattern.md) 사용하여 열을 그룹별로 제외한 모든 열에 규칙을 적용합니다.

![중복 제거](media/data-flow/agg-dedupe.png "중복 제거")

위의 예에서 `ProductID` 열은 `Name` 그룹화에 사용되고 있습니다. 두 행에 해당 열에 대해 동일한 값이 있는 경우 중복된 것으로 간주됩니다. 이 집계 변환에서는 일치하는 첫 번째 행의 값이 유지되고 다른 모든 행이 삭제됩니다. 열 패턴 구문을 사용하면 이름이 없는 `ProductID` 모든 `Name` 열이 기존 열 이름에 매핑되고 첫 번째 일치하는 행의 값이 부여됩니다. 출력 스키마는 입력 스키마와 동일합니다.

데이터 유효성 검사 `count()` 시나리오의 경우 함수를 사용하여 중복 항목 수를 계산할 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>예제

아래 예제에서는 들어오는 스트림을 `MoviesYear` 취하고 행을 열별로 `year`그룹화합니다. 변환은 열의 `avgrating` 평균으로 평가하는 집계 열을 `Rating`만듭니다. 이 집계 변환의 이름은 . `AvgComedyRatingsByYear`

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![예제별로 그룹화](media/data-flow/agg-script1.png "예제별로 그룹화")

![집계 예제](media/data-flow/agg-script2.png "집계 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![데이터 흐름 스크립트 집계](media/data-flow/aggdfs1.png "데이터 흐름 스크립트 집계")

```MoviesYear```: 파생된 열 정의 연도 ```AvgComedyRatingByYear```및 제목 열 : 연도별로 ```avgrating```그룹화된 코미디의 평균 등급에 대한 집계 변환 : 집계 된 값을 보유하도록 생성되는 새 열의 이름

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>다음 단계

* [창 변환을](data-flow-window.md) 사용하여 창 기반 집계 정의
