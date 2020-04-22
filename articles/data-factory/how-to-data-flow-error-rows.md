---
title: Azure 데이터 팩터리에서 데이터 흐름을 매핑하는 오류 행 처리
description: 매핑 데이터 흐름을 사용하여 Azure Data Factory에서 SQL 잘림 오류를 처리하는 방법을 알아봅니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732695"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>데이터 팩터리 매핑 데이터 흐름에서 SQL 잘림 오류 행 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름을 사용할 때 Data Factory의 일반적인 시나리오는 변환된 데이터를 Azure SQL 데이터베이스에 작성하는 것입니다. 이 시나리오에서는 방지 해야 하는 일반적인 오류 조건은 열 잘림이 가능 합니다. 다음 단계에 따라 대상 문자열 열에 맞지 않는 열로깅을 제공하여 이러한 시나리오에서 데이터 흐름을 계속할 수 있도록 합니다.

## <a name="scenario"></a>시나리오

1. "name"이라는 ```nvarchar(5)``` 열이 있는 대상 Azure SQL 데이터베이스 테이블이 있습니다.

2. 데이터 흐름 내부에서는 싱크대에서 대상 "이름" 열에 영화 제목을 매핑하려고 합니다.

    ![동영상 데이터 흐름 1](media/data-flow/error4.png)
    
3. 문제는 영화 제목이 5자만 보유할 수 있는 싱크 열 에 모두 맞지 않는다는 것입니다. 이 데이터 흐름을 실행하면 다음과 같은 오류가 발생합니다.```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

이 비디오에서는 데이터 흐름에서 설정 오류 행 처리 논리의 예를 안내합니다.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>이 조건을 중심으로 설계하는 방법

1. 이 시나리오에서 "name" 열의 최대 길이는 5자입니다. 따라서 5자 보다 긴 "titles"로 행을 기록하는 동시에 해당 공간에 들어갈 수 있는 나머지 행이 데이터베이스에 쓸 수 있도록 하는 조건부 분할 변환을 추가해 보겠습니다.

    ![조건부 분할(conditional split)](media/data-flow/error1.png)

2. 이 조건부 분할 변환은 "title"의 최대 길이를 5로 정의합니다. 5보다 적거나 같은 행은 ```GoodRows``` 스트림으로 이동합니다. 5보다 큰 행은 스트림으로 ```BadRows``` 이동합니다.

3. 이제 실패한 행을 기록해야 합니다. 로깅을 위해 ```BadRows``` 스트림에 싱크 변환을 추가합니다. 여기서는 전체 트랜잭션 레코드를 로깅할 수 있도록 모든 필드를 "자동 매핑"합니다. 이것은 Blob Storage의 단일 파일에 대한 텍스트 구분 CSV 파일 출력입니다. 로그 파일을 "badrows.csv"라고 부릅니다.

    ![잘못된 행](media/data-flow/error3.png)
    
4. 완료된 데이터 흐름은 다음과 같습니다. 이제 오류 행을 분리하여 SQL 잘림 오류를 방지하고 해당 항목을 로그 파일에 넣을 수 있습니다. 한편 성공적인 행은 대상 데이터베이스에 계속 쓸 수 있습니다.

    ![전체 데이터 흐름](media/data-flow/error2.png)

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름 변환을 사용하여 나머지 데이터 흐름 [논리를 빌드합니다.](concepts-data-flow-overview.md)
