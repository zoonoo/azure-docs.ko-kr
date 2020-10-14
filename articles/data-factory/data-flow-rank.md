---
title: 매핑 데이터 흐름의 순위 변환
description: Azure Data Factory를 사용 하는 방법 데이터 흐름 순위 변환에서 순위 열 생성
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: 8584d1b64191cc5258c6eeeef9ae4125bf1a2c65
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044737"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 순위 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Rank 변환을 사용 하 여 사용자가 지정한 정렬 조건에 따라 정렬 된 순위를 생성 합니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>구성

![순위 설정](media/data-flow/rank-configuration.png "순위 설정")

**대/소문자 구분 안 함:** 정렬 열이 문자열 형식인 경우 사례는 순위에 해당 합니다. 

**조밀한:** 사용 하도록 설정 된 경우 순위 열이 조밀한 순위를 갖습니다. 각 순위 개수는 연속 되는 숫자 이며, 연결 후에는 순위 값을 건너뛰지 않습니다.

**순위 열:** 생성 된 순위 열의 이름입니다. 이 열은 long 유형입니다.

**정렬 조건:** 정렬 기준이 되는 열 및 정렬 순서를 선택 합니다. 순서는 정렬 우선 순위를 결정 합니다.

위의 구성은 들어오는 농구 데이터를 사용 하 고 ' pointsRanking ' 라는 순위 열을 만듭니다. 열 *포인트* 값이 가장 높은 행의 *pointsranking* 값은 1입니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>예제

![순위 설정](media/data-flow/rank-configuration.png "순위 설정")

위의 순위 구성에 대 한 데이터 흐름 스크립트는 다음 코드 조각에 있습니다.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>다음 단계

[필터 변환을](data-flow-filter.md)사용 하 여 순위 값을 기준으로 행을 필터링 합니다.
