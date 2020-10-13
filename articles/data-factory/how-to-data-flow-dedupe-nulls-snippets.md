---
title: 데이터 흐름 조각을 사용 하 여 행 중복 제거 및 null 찾기
description: 데이터 흐름에서 코드 조각을 사용 하 여 행을 쉽게 중복 제거 하 고 null을 찾는 방법을 알아봅니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666503"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>데이터 흐름 조각을 사용 하 여 행 중복 제거 및 null 찾기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 매핑에서 코드 조각을 사용 하 여 데이터 중복 제거 및 null 필터링과 같은 일반적인 작업을 매우 쉽게 수행할 수 있습니다. 이 방법 가이드에서는 데이터 흐름 스크립트 조각을 사용 하 여 파이프라인에 이러한 함수를 매우 쉽게 추가 하는 방법을 설명 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **+ 새 파이프라인** 을 선택 하 여 새 파이프라인을 만듭니다.

2. 데이터 흐름 작업을 추가 합니다.

3. 원본 변환을 추가 하 고 데이터 집합 중 하나에 연결 합니다.

    ![소스 코드 조각 2](media/data-flow/snippet-adf-2.png)

4. 중복 제거 및 NULL 검사 조각은 데이터 흐름 스키마 드리프트를 활용 하는 일반 패턴을 사용 하 여 데이터 집합의 모든 스키마 또는 미리 정의 된 스키마가 없는 데이터 집합으로 작동 합니다.

5. [데이터 흐름 스크립트 설명서 페이지로 이동 하 여 개별 행에 대 한 코드 조각을 복사 합니다.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. 데이터 흐름 디자이너 UI에서 오른쪽 위의 스크립트 단추를 클릭 하 여 데이터 흐름 그래프 뒤에 있는 스크립트 편집기를 엽니다.

    ![소스 코드 조각 3](media/data-flow/snippet-adf-3.png)

7. 스크립트의에 대 한 정의 뒤에 ```source1``` Enter 키를 누른 다음 코드 조각에 붙여넣습니다.

8. 붙여넣은 코드 앞에 "source1"를 입력 하 여이 붙여넣은 코드 조각을 그래프에서 만든 이전 소스 변환에 연결 합니다.

9. 또는 그래프의 새 변환 노드에서 들어오는 스트림을 선택 하 여 디자이너에서 새 변환을 연결할 수 있습니다.

    ![소스 코드 조각 4](media/data-flow/snippet-adf-4.png)

10. 이제 데이터 흐름은 모든 열 값에서 일반 해시를 사용 하 여 모든 행을 기준으로 그룹화 하는 집계 변환을 사용 하 여 원본에서 중복 행을 제거 합니다.
    
11. 다음으로, Null을 포함 하는 행과 Null이 없는 스트림이 포함 된 스트림으로 데이터를 분할 하는 코드 조각을 추가 합니다.

12. [조각 라이브러리로 돌아가서 NULL 검사를 위해 코드를 복사 합니다.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. 데이터 흐름 디자이너에서 스크립트를 다시 클릭 하 고 맨 아래에 새 변환 코드를 붙여넣고 붙여 넣은 코드 조각 앞에 해당 변환의 이름을 입력 하 여 이전 변환에 연결 합니다.

14. 이제 데이터 흐름 그래프가 다음과 같이 표시 됩니다.

    ![소스 코드 조각 1](media/data-flow/snippet-adf-1.png)

  이제 데이터 흐름 스크립트 라이브러리에서 기존 코드 조각을 가져와 기존 디자인에 추가 하 여 제네릭 deduping 및 NULL 검사를 사용 하 여 데이터 흐름을 정상적으로 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
