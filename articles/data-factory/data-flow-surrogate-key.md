---
title: 매핑 데이터 흐름의 서로게이트 키 변환
description: Azure Data Factory의 데이터 흐름의 매핑 서로게이트 키 변환을 사용 하 여 순차적 키 값을 생성 하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147187"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 서로게이트 키 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

서로게이트 키 변환을 사용 하 여 데이터의 각 행에 증분 키 값을 추가 합니다. 이 기능은 별모양 스키마 분석 데이터 모델에서 차원 테이블을 디자인할 때 유용 합니다. 별모양 스키마에서 차원 테이블의 각 멤버에는 비 비즈니스 키인 고유 키가 필요 합니다.

## <a name="configuration"></a>Configuration

![서로게이트 키 변환](media/data-flow/surrogate.png "서로게이트 키 변환")

**키 열:** 생성 된 서로게이트 키 열의 이름입니다.

**시작 값:** 가장 작은 키 값이 생성 됩니다.

## <a name="increment-keys-from-existing-sources"></a>기존 원본에서 키 증가

원본에 있는 값에서 시퀀스를 시작 하려면 캐시 싱크를 사용 하 여 해당 값을 저장 하 고 파생 열 변환을 사용 하 여 두 값을 함께 추가 하는 것이 좋습니다. 캐시 된 조회를 사용 하 여 출력을 가져오고 생성 된 키에 추가 합니다. 자세한 내용은 [캐시 싱크](data-flow-sink.md#cache-sink) 및 [캐시 된 조회](concepts-data-flow-expression-builder.md#cached-lookup)에 대해 알아봅니다.

![서로게이트 키 조회](media/data-flow/cached-lookup-example.png "서로게이트 키 조회")

### <a name="increment-from-existing-maximum-value"></a>기존 최 댓 값에서 증가

키 값을 이전 max로 초기값으로 설정 하려면 원본 데이터가 있는 위치에 따라 두 가지 방법을 사용할 수 있습니다.

#### <a name="database-sources"></a>데이터베이스 원본

SQL 쿼리 옵션을 사용 하 여 원본에서 MAX ()를 선택 합니다. 예: `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![서로게이트 키 쿼리](media/data-flow/surrogate-key-max-database.png "서로게이트 키 변환 쿼리")

#### <a name="file-sources"></a>파일 원본

이전 max 값이 파일에 있으면 `max()` 집계 변환에서 함수를 사용 하 여 이전 최대값을 가져옵니다.

![서로게이트 키 파일](media/data-flow/surrogate-key-max-file.png "서로게이트 키 파일")

두 경우 모두 캐시 싱크에 쓰고 값을 조회 해야 합니다. 


## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>예제

![서로게이트 키 변환](media/data-flow/surrogate.png "서로게이트 키 변환")

위의 서로게이트 키 구성에 대 한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>다음 단계

이 예에서는 [조인](data-flow-join.md) 및 [파생 열](data-flow-derived-column.md) 변환을 사용 합니다.
