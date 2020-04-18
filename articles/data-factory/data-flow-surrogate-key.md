---
title: 매핑 데이터 흐름의 대리 키 변환
description: Azure Data Factory의 매핑 데이터 흐름 대리 키 변환을 사용하여 순차키 값을 생성하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606289"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 대리 키 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

서로게이트 키 변환을 사용하여 각 데이터 행에 증분 키 값을 추가합니다. 이 기능은 별표 스키마 분석 데이터 모델에서 치수 테이블을 디자인할 때 유용합니다. 스타 스키마에서 차원 테이블의 각 구성원에는 비비즈니스 키인 고유한 키가 필요합니다.

## <a name="configuration"></a>구성

![서로게이트 키 변환](media/data-flow/surrogate.png "대리 키 변환")

**키 열:** 생성된 서로게이트 키 열의 이름입니다.

**시작 값:** 생성될 최저 키 값입니다.

## <a name="increment-keys-from-existing-sources"></a>기존 소스의 키 증분

소스에 있는 값에서 시퀀스를 시작하려면 서로게이트 키 변환 다음파생 열 변환을 사용하여 두 값을 함께 추가합니다.

![SK, 최대 추가](media/data-flow/sk006.png "서로게이트 키 변환 최대 추가")

### <a name="increment-from-existing-maximum-value"></a>기존 최대값에서 증가

이전 max로 키 값을 시드하려면 원본 데이터의 위치에 따라 사용할 수 있는 두 가지 기술이 있습니다.

#### <a name="database-sources"></a>데이터베이스 원본

SQL 쿼리 옵션을 사용하여 소스에서 MAX()를 선택합니다. 예를 들어`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![대리 키 쿼리](media/data-flow/sk002.png "대리 키 변환 쿼리")

#### <a name="file-sources"></a>파일 소스

이전 최대 값이 파일에 있는 경우 `max()` 집계 변환의 함수를 사용하여 이전 최대 값을 가져옵니다.

![서로게이트 키 파일](media/data-flow/sk008.png "서로게이트 키 파일")

두 경우 모두 들어오는 새 데이터를 이전 최대 값을 포함하는 원본과 함께 조인해야 합니다.

![서로게이트 키 조인](media/data-flow/sk004.png "서로게이트 키 조인")

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

![서로게이트 키 변환](media/data-flow/surrogate.png "대리 키 변환")

위의 서로게이트 키 구성에 대한 데이터 흐름 스크립트는 아래 코드 코드 조각에 있습니다.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>다음 단계

이러한 예제는 [조인](data-flow-join.md) 및 [파생 열](data-flow-derived-column.md) 변환을 사용합니다.
