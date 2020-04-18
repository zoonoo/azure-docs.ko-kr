---
title: 매핑 데이터 흐름의 필터 변환
description: Azure Data Factory 매핑 데이터 흐름의 필터 변환을 사용하여 행 필터링
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606432"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 필터 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

필터 변환은 조건을 기반으로 행 필터링을 허용합니다. 출력 스트림에는 필터링 조건과 일치하는 모든 행이 포함됩니다. 필터 변환은 SQL의 WHERE 절과 유사합니다.

## <a name="configuration"></a>구성

데이터 흐름 식 빌더를 사용하여 필터 조건에 대한 식을 입력합니다. 식 작성기를 열려면 파란색 상자를 클릭합니다. 필터 조건은 부울 형식이어야 합니다. 식을 만드는 방법에 대한 자세한 내용은 [식 작성기](concepts-data-flow-expression-builder.md) 설명서를 참조하십시오.

![필터 변환](media/data-flow/filter1.png "필터 변환")

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림을 `FilterBefore1960` `CleanData`받는 필터 변환입니다. 필터 조건은 식입니다. `year <= 1960`

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![필터 변환](media/data-flow/filter1.png "필터 변환")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>다음 단계

[선택 변환으로](data-flow-select.md) 열 필터링
