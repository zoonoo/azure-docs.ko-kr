---
title: 매핑 데이터 흐름의 Alter row 변환
description: 매핑 데이터 흐름의 alter row 변환을 사용 하 여 데이터베이스 대상을 업데이트 하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982652"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 Alter row 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

행에 대 한 insert, delete, update 및 upsert 정책을 설정 하려면 행 변경 변환을 사용 합니다. 일대다 조건을 식으로 추가할 수 있습니다. 이러한 조건은 각 행이 첫 번째 일치 식에 해당 하는 정책으로 표시 되므로 우선 순위에 따라 지정 해야 합니다. 이러한 각 조건은 행 (또는 행)이 삽입, 업데이트, 삭제 또는 upserted에 게 발생할 수 있습니다. Alter Row는 데이터베이스에 대해 DDL & DML 작업을 모두 생성할 수 있습니다.

![행 설정 변경](media/data-flow/alter-row1.png "행 설정 변경")

Alter Row 변환은 데이터 흐름의 데이터베이스 또는 CosmosDB 싱크에 대해서만 작동 합니다. 행에 할당 하는 작업 (insert, update, delete, upsert)은 디버그 세션 중에 발생 하지 않습니다. 파이프라인에서 데이터 흐름 실행 작업을 실행 하 여 데이터베이스 테이블에 대 한 alter row 정책을 적용 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>기본 행 정책 지정

Alter Row 변환을 만들고 조건이 인 행 정책을 지정 `true()` 합니다. 이전에 정의 된 식과 일치 하지 않는 각 행은 지정 된 행 정책에 대해 표시 됩니다. 기본적으로 조건 식과 일치 하지 않는 각 행은로 표시 됩니다 `Insert` .

![행 정책 변경](media/data-flow/alter-row4.png "행 정책 변경")

> [!NOTE]
> 모든 행을 하나의 정책으로 표시 하려면 해당 정책에 대 한 조건을 만들고 조건을로 지정 하면 `true()` 됩니다.

## <a name="view-policies-in-data-preview"></a>데이터 미리 보기에서 정책 보기

[디버그 모드](concepts-data-flow-debug-mode.md) 를 사용 하 여 데이터 미리 보기 창에서 alter row 정책의 결과를 볼 수 있습니다. 행 변경 변환의 데이터 미리 보기는 대상에 대해 DDL 또는 DML 작업을 생성 하지 않습니다.

![행 정책 변경](media/data-flow/alter-row3.png "행 정책 변경")

각 alter row 정책은 insert, update, upsert 또는 deleted 동작이 발생 하는지 여부를 나타내는 아이콘으로 표시 됩니다. Top 헤더는 미리 보기에서 각 정책의 영향을 받는 행 수를 보여 줍니다.

## <a name="allow-alter-row-policies-in-sink"></a>싱크에서 alter row 정책 허용

Alter row 정책이 작동 하려면 데이터 스트림이 데이터베이스 또는 Cosmos 싱크에 써야 합니다. 싱크의 **설정** 탭에서 해당 싱크에 대해 허용 되는 alter row 정책을 사용 하도록 설정 합니다.

![행 싱크 변경](media/data-flow/alter-row2.png "행 싱크 변경")

기본 동작은 삽입만 허용 하는 것입니다. 업데이트, upsert 또는 삭제를 허용 하려면 해당 조건에 해당 하는 싱크에 있는 상자를 선택 합니다. 업데이트, upsert 또는 삭제를 사용 하도록 설정한 경우 싱크에 일치 시킬 키 열을 지정 해야 합니다.

> [!NOTE]
> 삽입, 업데이트 또는 upsert가 싱크에 있는 대상 테이블의 스키마를 수정 하면 데이터 흐름이 실패 합니다. 데이터베이스의 대상 스키마를 수정 하려면 테이블 **다시 만들기** 동작을 선택 합니다. 그러면 새 스키마 정의를 사용 하 여 테이블을 삭제 하 고 다시 만듭니다.

싱크 변환에는 대상 데이터베이스에서 고유한 행 id에 대 한 단일 키 또는 일련의 키가 필요 합니다. SQL 싱크에 대해 싱크 설정 탭에서 키를 설정 합니다. CosmosDB의 경우 설정에서 파티션 키를 설정 하 고 싱크 매핑에서 CosmosDB 시스템 필드 "id"도 설정 합니다. CosmosDB의 경우 업데이트, upsert 및 삭제에 대 한 시스템 열 "id"를 반드시 포함 해야 합니다.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Azure SQL Database 및 Synapse를 사용 하 여 병합 및 upsert

ADF 데이터 흐름은 upsert 옵션을 사용 하 여 Azure SQL Database 및 Synapse Database 풀 (데이터 웨어하우스)에 대 한 병합을 지원 합니다.

그러나 대상 데이터베이스 스키마에서 키 열의 identity 속성을 사용한 시나리오를 실행할 수 있습니다. ADF를 사용 하려면 업데이트 및 upsert에 대 한 행 값과 일치 하는 데 사용할 키를 식별 해야 합니다. 하지만 대상 열에 identity 속성이 설정 되어 있고 upsert 정책을 사용 하는 경우 대상 데이터베이스에서 열에 쓰기를 허용 하지 않습니다. 분산 테이블의 배포 열에 대해 upsert를 시도 하는 경우 오류가 발생할 수도 있습니다.

다음은이 문제를 해결 하는 방법입니다.

1. 싱크 변환 설정으로 이동 하 고 "키 열 쓰기 생략"을 설정 합니다. 그러면 ADF가 매핑에 대 한 키 값으로 선택한 열을 쓰지 않습니다.

2. 해당 키 열이 id 열에 대 한 문제를 일으키는 열이 아닌 경우 싱크 변환 전처리 SQL 옵션을 사용할 수 ```SET IDENTITY_INSERT tbl_content ON``` 있습니다. 그런 다음 사후 처리 SQL 속성을 사용 하 여 해제 합니다. ```SET IDENTITY_INSERT tbl_content OFF```

3. Id 사례와 배포 열의 경우 모두, 조건부 분할 변환을 사용 하 여 별도의 업데이트 조건과 별도의 삽입 조건을 사용 하 여 Upsert에서 논리를 전환할 수 있습니다. 이러한 방식으로 키 열 매핑을 무시 하도록 업데이트 경로에 대 한 매핑을 설정할 수 있습니다.

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

아래 예는 `CleanData` 들어오는 스트림을 사용 하 `SpecifyUpsertConditions` 고 3 개의 alter row 조건을 만드는 라는 alter row 변환입니다. 이전 변환에서는 `alterRowCondition` 데이터베이스에서 행이 삽입, 업데이트 또는 삭제 되는지 여부를 결정 하는 라는 열이 계산 됩니다. 열 값에 alter row 규칙과 일치 하는 문자열 값이 있으면 해당 정책이 할당 됩니다.

Data Factory UX에서 이 변환은 아래 이미지와 같습니다.

![Alter row 예](media/data-flow/alter-row4.png "Alter row 예")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>다음 단계

Alter Row 변환 후에는 [데이터를 대상 데이터 저장소로 싱크로](data-flow-sink.md)지정할 수 있습니다.
