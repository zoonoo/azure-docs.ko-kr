---
title: 매핑 데이터 흐름의 순위 변환
description: Azure Data Factory의 매핑 데이터 흐름 순위 변환을 사용하는 순위 열 생성 방법
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: bc0a4d8701f8d53f0acff362713054269378a88a
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903743"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 순위 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

순위 변환을 사용하여 사용자가 지정한 정렬 조건에 따라 정렬된 순위를 생성합니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>구성

![순위 설정](media/data-flow/rank-configuration.png "순위 설정")

**대/소문자 구분 안 함:** 정렬 열이 문자열 형식인 경우 대/소문자가 순위 지정에 반영됩니다. 

**조밀성:** 사용하도록 설정된 경우 순위 열이 조밀성 순위를 가집니다. 각 순위 개수는 연속되는 숫자이며, 연결 후에는 순위 값을 건너뛰지 않습니다.

**순위 열:** 생성된 순위 열의 이름입니다. 이 열은 long 형식입니다.

**정렬 조건:** 정렬 기준이 되는 열 및 정렬 순서를 선택합니다. 순서는 정렬 우선 순위를 결정합니다.

위의 구성은 수신 농구 데이터를 사용하고 ‘pointsRanking’이라는 순위 열을 만듭니다. *PTS* 열의 값이 가장 높은 행의 *pointsRanking* 값은 1입니다.

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

위의 순위 구성에 대한 데이터 흐름 스크립트는 아래 코드 조각에 나와 있습니다.

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

[필터 변환](data-flow-filter.md)을 사용하여 순위 값을 기준으로 행을 필터링합니다.
