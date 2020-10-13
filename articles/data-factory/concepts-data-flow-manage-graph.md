---
title: 매핑 데이터 흐름 그래프 관리
description: 매핑 데이터 흐름 그래프를 효과적으로 관리 하 고 편집 하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 0cdad47123d69ca7cee468c5bb0cea3268d73bfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420114"
---
# <a name="managing-the-mapping-data-flow-graph"></a>매핑 데이터 흐름 그래프 관리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 매핑은 데이터 흐름 그래프로 알려진 디자인 화면을 사용 하 여 작성 됩니다. 그래프에서 변환 논리는 왼쪽에서 오른쪽으로 빌드되고 추가 데이터 스트림은 하향식으로 추가 됩니다. 새 변환을 추가 하려면 기존 변환의 오른쪽 아래에 있는 더하기 기호를 선택 합니다.

![캔버스](media/data-flow/canvas2.png "캔버스")

데이터 흐름이 더 복잡해 지 면 다음 메커니즘을 사용 하 여 데이터 흐름 그래프를 효과적으로 탐색 하 고 관리할 수 있습니다. 

## <a name="moving-transformations"></a>변환 이동

데이터 흐름 매핑에서는 일련의 연결 된 변환 논리를 **스트림으로**인식 합니다. **들어오는 스트림** 필드는 현재 변환을 공급 하는 데이터 스트림을 나타냅니다. 각 변환에는 함수에 따라 하나 또는 두 개의 들어오는 스트림이 있고 출력 스트림을 나타냅니다. 들어오는 스트림의 출력 스키마는 현재 변환에서 참조할 수 있는 열 메타 데이터를 결정 합니다.

![노드 이동](media/data-flow/move-nodes.png "노드 이동")

파이프라인 canvas와 달리 데이터 흐름 변환은 끌어서 놓기 모델을 사용 하 여 편집 되지 않습니다. 의 들어오는 스트림 또는 "이동" 변환을 변경 하려면 **들어오는 스트림** 드롭다운에서 다른 값을 선택 합니다. 이 작업을 수행 하면 모든 다운스트림 변환이 편집한 변환과 함께 이동 합니다. 그래프는 새 논리 흐름을 표시 하도록 자동으로 업데이트 됩니다. 들어오는 스트림을 이미 다운스트림 변환이 있는 변환으로 변경 하면 새 분기 또는 병렬 데이터 스트림이 생성 됩니다. [데이터 흐름 매핑의 새 분기](data-flow-new-branch.md)에 대해 자세히 알아보세요.

## <a name="hide-graph-and-show-graph"></a>그래프 숨기기 및 그래프 표시

변환을 편집할 때 구성 패널을 확장 하 여 전체 캔버스를 사용 하 여 그래프를 숨길 수 있습니다. 캔버스의 오른쪽에 있는 위쪽 펼침 단추를 클릭 합니다.

![그래프 숨기기](media/data-flow/hide-graph.png "그래프 숨기기")

그래프가 숨겨진 경우에는 **다음** 또는 **이전**을 클릭 하 여 스트림 내에서 변환 간을 이동할 수 있습니다. 아래쪽 펼침 단추를 클릭 하 여 그래프를 표시 합니다.

![그래프 표시](media/data-flow/show-graph.png "그래프 표시")

## <a name="searching-for-transformations"></a>변환 검색

그래프에서 변환을 신속 하 게 찾으려면 확대/축소 설정 위의 **검색** 아이콘을 클릭 합니다.

![검색](media/data-flow/search-1.png "그래프 검색")

변환 이름 또는 설명을 기준으로 검색 하 여 변환을 찾을 수 있습니다.

![검색](media/data-flow/search-2.png "그래프 검색")

## <a name="hide-reference-nodes"></a>참조 노드 숨기기

데이터 흐름에 조인, 조회, 존재 또는 union 변환이 있는 경우 데이터 흐름에는 들어오는 모든 스트림에 대 한 참조 노드가 표시 됩니다. 사용 된 세로 공간 크기를 최소화 하려는 경우 참조 노드를 최소화할 수 있습니다. 이렇게 하려면 캔버스를 마우스 오른쪽 단추로 클릭 하 고 **참조 노드 숨기기**를 선택 합니다.

![참조 노드 숨기기](media/data-flow/hide-reference-nodes.png "참조 노드 숨기기")

## <a name="next-steps"></a>다음 단계

데이터 흐름 논리를 완료 한 후 [디버그 모드](concepts-data-flow-debug-mode.md) 를 설정 하 고 데이터 미리 보기에서 테스트 합니다.
