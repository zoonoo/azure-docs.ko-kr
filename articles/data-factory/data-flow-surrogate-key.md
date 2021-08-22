---
title: 매핑 데이터 흐름의 서로게이트 키 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 매핑 데이터 흐름 서로게이트 키 변환을 사용하여 순차 키 값을 생성하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/30/2020
ms.openlocfilehash: 542cda8f785727a9736f286eb88bacb8caa0f58c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642522"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 서로게이트 키 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

서로게이트 키 변환을 사용하여 데이터의 각 행에 증분 키 값을 추가합니다. 이는 별모양 스키마 분석 데이터 모델에서 차원 테이블을 디자인할 때 유용합니다. 별모양 스키마에서 차원 테이블의 각 구성원에는 비즈니스 키가 아닌 고유 키가 필요합니다.

## <a name="configuration"></a>구성

![서로게이트 키 변환](media/data-flow/surrogate.png "서로게이트 키 변환")

**키 열:** 생성된 서로게이트 키 열의 이름입니다.

**시작 값:** 생성될 가장 낮은 키 값입니다.

## <a name="increment-keys-from-existing-sources"></a>기존 소스의 키 증가

소스에 있는 값에서 시퀀스를 시작하려면 캐시 싱크를 사용하여 해당 값을 저장하고 파생 열 변환을 사용하여 두 값을 함께 추가하는 것이 좋습니다. 캐시된 조회를 사용하여 출력을 가져와 생성된 키에 추가합니다. 자세한 내용은 [캐시 싱크](data-flow-sink.md#cache-sink) 및 [캐시된 조회](concepts-data-flow-expression-builder.md#cached-lookup)에 대해 알아봅니다.

![서로게이트 키 조회](media/data-flow/cached-lookup-example.png "서로게이트 키 조회")

### <a name="increment-from-existing-maximum-value"></a>기존 최댓값에서 증분

키 값을 이전 최댓값으로 시드하기 위해 원본 데이터의 위치에 따라 사용할 수 있는 두 가지 기술이 있습니다.

#### <a name="database-sources"></a>데이터베이스 원본

SQL 쿼리 옵션을 사용하여 원본에서 MAX()를 선택합니다. 예들 들어 `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`입니다.

![서로게이트 키 쿼리](media/data-flow/surrogate-key-max-database.png "서로게이트 키 변환 쿼리")

#### <a name="file-sources"></a>파일 원본

이전 최댓값이 파일에 있는 경우 집계 변환에서 `max()` 함수를 사용하여 이전 최댓값을 가져옵니다.

![서로게이트 키 파일](media/data-flow/surrogate-key-max-file.png "서로게이트 키 파일")

두 경우 모두 캐시 싱크에 쓰고 값을 조회해야 합니다. 


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

위의 서로게이트 키에 대한 데이터 흐름 스크립트는 아래 코드 조각에 나와 있습니다.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>다음 단계

이 예에서는 [조인](data-flow-join.md) 및 [파생 열](data-flow-derived-column.md) 변환을 사용합니다.
