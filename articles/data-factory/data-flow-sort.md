---
title: 매핑 데이터 흐름의 정렬 변환
description: Azure Data Factory 매핑 데이터 정렬 변환
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606333"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 정렬 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

정렬 변환을 사용 하 여 현재 데이터 스트림의 들어오는 행을 정렬할 수 있습니다. 개별 열을 선택 하 고 오름차순 이나 내림차순으로 정렬할 수 있습니다.

> [!NOTE]
> 데이터 흐름 매핑은 여러 노드 및 파티션에 데이터를 분산 하는 spark 클러스터에서 실행 됩니다. 이후 변환에서 데이터를 다시 분할 하도록 선택 하는 경우 데이터 단지 섞는으로 인해 정렬이 손실 될 수 있습니다.

## <a name="configuration"></a>Configuration

![정렬 설정](media/data-flow/sort.png "정렬")

**대/소문자 구분 안 함:** 문자열이 나 텍스트 필드를 정렬할 때 대/소문자를 무시할지 여부

**파티션 내 에서만 정렬:** 데이터 흐름이 spark에서 실행 될 때 각 데이터 스트림은 파티션으로 나뉩니다. 이 설정은 전체 데이터 스트림을 정렬 하지 않고 들어오는 파티션 내 에서만 데이터를 정렬 합니다. 

**정렬 조건:** 정렬 기준이 되는 열 및 정렬 순서를 선택 합니다. 순서는 정렬 우선 순위를 결정 합니다. 데이터 스트림의 시작 부분 또는 끝 부분에 null을 표시할지 여부를 선택 합니다.

### <a name="computed-columns"></a>계산 열

정렬을 적용 하기 전에 열 값을 수정 하거나 추출 하려면 열 위로 마우스를 이동 하 고 "계산 열"을 선택 합니다. 그러면 식 작성기가 열 값을 사용 하는 대신 정렬 작업에 대 한 식을 만듭니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>예제

![정렬 설정](media/data-flow/sort.png "정렬")

위의 정렬 구성에 대 한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>다음 단계

정렬 후 [집계 변환을](data-flow-aggregate.md) 사용 하는 것이 좋습니다.
