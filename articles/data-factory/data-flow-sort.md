---
title: 매핑 데이터 흐름의 정렬 변환
description: Azure 데이터 팩터리 매핑 데이터 정렬 변환
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606333"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 정렬 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

정렬 변환을 사용하면 현재 데이터 스트림에서 들어오는 행을 정렬할 수 있습니다. 개별 열을 선택하고 오름차순 또는 내림차순으로 정렬할 수 있습니다.

> [!NOTE]
> 매핑 데이터 흐름은 여러 노드와 파티션에 걸쳐 데이터를 분산하는 스파크 클러스터에서 실행됩니다. 후속 변환에서 데이터를 다시 분할하도록 선택하면 데이터 전환으로 인해 정렬이 손실될 수 있습니다.

## <a name="configuration"></a>구성

![정렬 설정](media/data-flow/sort.png "정렬")

**대/소문자 구분:** 문자열 또는 텍스트 필드를 정렬할 때 대/소문자를 무시할지 여부

**파티션 내에서만 정렬:** 스파크에서 데이터 흐름이 실행되면 각 데이터 스트림은 파티션으로 나뉩니다. 이 설정은 전체 데이터 스트림을 정렬하는 대신 들어오는 파티션 내에서만 데이터를 정렬합니다. 

**정렬 조건:** 정렬할 열과 정렬 순서를 선택합니다. 순서에 따라 정렬 우선 순위가 결정됩니다. 데이터 스트림의 시작 또는 끝에 null이 나타날지 여부를 선택합니다.

### <a name="computed-columns"></a>계산 열

정렬을 적용하기 전에 열 값을 수정하거나 추출하려면 열 위로 마우스를 가져가서 "계산된 열"을 선택합니다. 그러면 식 빌더가 열리고 열 값을 사용하는 대신 정렬 작업에 대한 식을 만듭니다.

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

위의 정렬 구성에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>다음 단계

정렬 후 [집계 변환을](data-flow-aggregate.md) 사용할 수 있습니다.
