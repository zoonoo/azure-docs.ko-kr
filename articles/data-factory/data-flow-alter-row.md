---
title: 매핑 데이터 흐름의 행 변환 변경
description: 매핑 데이터 흐름에서 행 변환 을 변경 하여 데이터베이스 대상을 업데이트하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834532"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 행 변환 변경

행 변경 변환을 사용하여 행에 삽입, 삭제, 업데이트 및 upsert 정책을 설정합니다. 일대다 조건을 식으로 추가할 수 있습니다. 각 행은 첫 번째 일치 식에 해당하는 정책으로 표시되기 때문에 이러한 조건은 우선 순위순으로 지정해야 합니다. 이러한 각 조건은 행(또는 행)이 삽입, 업데이트, 삭제 또는 업시업될 수 있습니다. Alter Row는 데이터베이스에 대해 DDL & DML 작업을 모두 생성할 수 있습니다.

![행 설정 변경](media/data-flow/alter-row1.png "행 설정 변경")

행 변경 변경 변경은 데이터 흐름의 데이터베이스 또는 CosmosDB 싱크에서만 작동합니다. 디버그 세션 중에는 행에 할당하는 작업(삽입, 업데이트, 삭제, upsert)이 발생하지 않습니다. 파이프라인에서 데이터 흐름 실행 작업을 실행하여 데이터베이스 테이블의 행 변경 정책을 제정합니다.

## <a name="specify-a-default-row-policy"></a>기본 행 정책 지정

행 변경 변환을 만들고 `true()`의 조건을 사용하여 행 정책을 지정합니다. 이전에 정의된 식과 일치하지 않는 각 행은 지정된 행 정책에 대해 표시됩니다. 기본적으로 조건식과 일치하지 않는 각 행은 에 대해 `Insert`표시됩니다.

![행 정책 변경](media/data-flow/alter-row4.png "행 정책 변경")

> [!NOTE]
> 모든 행을 하나의 정책으로 표시하려면 해당 정책에 대한 조건을 `true()`만들고 조건을 로 지정할 수 있습니다.

## <a name="view-policies-in-data-preview"></a>데이터 미리 보기에서 정책 보기

[디버그 모드를](concepts-data-flow-debug-mode.md) 사용하여 데이터 미리 보기 창에서 행 변경 정책의 결과를 볼 수 있습니다. 변경 행 변환의 데이터 미리 보기는 대상에 대한 DDL 또는 DML 작업을 생성하지 않습니다.

![행 정책 변경](media/data-flow/alter-row3.png "행 정책 변경")

각 alter 행 정책은 삽입, 업데이트, upsert 또는 삭제된 작업이 발생할지 여부를 나타내는 아이콘으로 표시됩니다. 위쪽 헤더에는 미리 보기의 각 정책의 영향을 받는 행 수가 표시됩니다.

## <a name="allow-alter-row-policies-in-sink"></a>싱크에서 행 정책 변경 허용

행 변경 정책이 작동하려면 데이터 스트림이 데이터베이스 또는 Cosmos 싱크에 기록되어야 합니다. 싱크의 **설정** 탭에서 해당 싱크에 허용되는 행 정책을 변경할 수 있도록 설정합니다.

![행 싱크 변경](media/data-flow/alter-row2.png "행 싱크 변경")

 기본 동작은 삽입만 허용하는 것입니다. 업데이트, upserts 또는 삭제를 허용하려면 해당 조건에 해당하는 싱크의 확인란을 선택합니다. 업데이트, upserts 또는 삭제가 활성화된 경우 싱크의 어떤 키 열과 일치할지 지정해야 합니다.

> [!NOTE]
> 삽입, 업데이트 또는 upserts가 싱크에서 대상 테이블의 스키마를 수정하면 데이터 흐름이 실패합니다. 데이터베이스에서 대상 스키마를 수정하려면 **테이블 만들기를** 테이블 작업으로 선택합니다. 이렇게 하면 새 스키마 정의로 테이블이 삭제되고 다시 생성됩니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림을 `CleanData` `SpecifyUpsertConditions` 가져와 세 개의 alter 행 조건을 만드는 이름의 alter 행 변환입니다. 이전 변환에서 명명된 `alterRowCondition` 열은 행이 데이터베이스에 삽입, 업데이트 또는 삭제되는지 여부를 결정하는 계산됩니다. 열의 값에 alter row 규칙과 일치하는 문자열 값이 있으면 해당 정책이 할당됩니다.

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![행 예제 변경](media/data-flow/alter-row4.png "행 예제 변경")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>다음 단계

행 변경 [이후에는 데이터를 대상 데이터 저장소에 싱크할](data-flow-sink.md)수 있습니다.
