---
title: 데이터 흐름 코드 조각을 사용 하 여 행 중복 제거 및 null 찾기
description: 데이터 흐름에서 코드 조각을 사용 하 여 행을 쉽게 중복 제거 하 고 null을 찾는 방법에 대해 알아봅니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: cdb522cc4be83eadd2c60c91c7fee33e7ccc039b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632450"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>데이터 흐름 코드 조각을 사용 하 여 행 중복 제거 및 null 찾기

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 매핑에서 코드 조각을 사용 하 여 데이터 중복 제거 및 null 필터링과 같은 일반적인 작업을 쉽게 수행할 수 있습니다. 이 문서에서는 데이터 흐름 스크립트 조각을 사용 하 여 파이프라인에 이러한 함수를 쉽게 추가 하는 방법을 설명 합니다.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **새 파이프라인** 을 선택 합니다.

1. 데이터 흐름 작업을 추가 합니다.

1. **소스 설정** 탭을 선택 하 고 원본 변환을 추가한 다음 데이터 집합 중 하나에 연결 합니다.

    ![원본 유형을 추가 하기 위한 "원본 설정" 창의 스크린샷](media/data-flow/snippet-adf-2.png)

    중복 제거 및 null 검사 조각은 데이터 흐름 스키마 드리프트를 활용 하는 일반 패턴을 사용 합니다. 이 코드 조각은 데이터 집합의 스키마 또는 미리 정의 된 스키마가 없는 데이터 집합을 사용 하 여 작업 합니다.

1. [데이터 흐름 스크립트 (DFS)](./data-flow-script.md#distinct-row-using-all-columns)의 "모든 열을 사용 하는 고유 행" 섹션에서 DistinctRows에 대 한 코드 조각을 복사 합니다.

1. [데이터 흐름 스크립트 설명서 페이지로 이동 하 여 개별 행에 대 한 코드 조각을 복사 합니다.](./data-flow-script.md#distinct-row-using-all-columns)

    ![소스 코드 조각의 스크린샷.](media/data-flow/snippet-adf-3.png)

1. 스크립트에서의 정의 뒤에 `source1` Enter 키를 누른 다음 코드 조각을 붙여넣습니다.

1. 다음 중 하나를 수행합니다.

   * 붙여넣은 코드 앞에 **source1** 를 입력 하 여이 붙여넣은 코드 조각을 그래프의 앞에서 만든 소스 변환에 연결 합니다.

   * 또는 그래프의 새 변환 노드에서 들어오는 스트림을 선택 하 여 디자이너에서 새 변환을 연결할 수 있습니다.

     !["조건부 분할 설정" 창의 스크린샷](media/data-flow/snippet-adf-4.png)

   이제 데이터 흐름은 모든 열 값에서 일반 해시를 사용 하 여 모든 행을 기준으로 그룹화 하는 집계 변환을 사용 하 여 원본에서 중복 행을 제거 합니다.
    
1. Null을 포함 하는 행과 다른 스트림을 포함 하는 하나의 스트림으로 데이터를 분할 하는 코드 조각을 추가 합니다. 이를 수행하려면:

1. [조각 라이브러리로 돌아가서 NULL 검사를 위해 코드를 복사 합니다.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. 데이터 흐름 디자이너에서 **스크립트** 를 다시 선택 하 고 맨 아래에이 새 변환 코드를 붙여넣습니다. 이 작업은 붙여 넣은 코드 조각 앞에 해당 변환의 이름을 배치 하 여 스크립트를 이전 변환에 연결 합니다.

   이제 데이터 흐름 그래프가 다음과 같이 표시 됩니다.

    ![데이터 흐름 그래프의 스크린샷](media/data-flow/snippet-adf-1.png)

  이제 데이터 흐름 스크립트 라이브러리에서 기존 코드 조각을 가져와 기존 디자인에 추가 하 여 일반 deduping 및 null 검사를 사용 하 여 작업 데이터 흐름을 만들었습니다.

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.