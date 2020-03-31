---
title: 데이터 흐름 그래프
description: 데이터 팩터리 데이터 흐름 그래프로 작업하는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928317"
---
# <a name="mapping-data-flow-graphs"></a>데이터 흐름 그래프 매핑

매핑 데이터 흐름 설계 표면은 하향식, 왼쪽에서 오른쪽으로 데이터 흐름을 빌드하는 "구성" 표면입니다. 더하기(+) 기호를 사용하여 각 변환에 연결된 도구 상자가 있습니다. 자유 형식 DAG 환경에서 가장자리를 통해 노드에 연결하는 대신 비즈니스 논리에 집중합니다.

다음은 데이터 흐름 그래프를 관리하는 기본 제공 메커니즘입니다.

## <a name="move-nodes"></a>이동 노드

![집계 변환 옵션](media/data-flow/agghead.png "애그리게이터 헤더")

끌어서 놓기 패러다임이 없으면 변환 노드를 "이동"하는 방법은 들어오는 스트림을 변경하는 것입니다. 대신 “들어오는 스트림”변경하여 변환을 이동하게 됩니다.

## <a name="streams-of-data-inside-of-data-flow"></a>데이터 흐름 내부의 데이터 스트림

Azure Data Factory 데이터 흐름에서 스트림은 데이터의 흐름을 나타냅니다. 변환 설정 창에 "들어오는 스트림" 필드가 표시됩니다. 이는 들어오는 데이터 스트림이 해당 변환에 공급되었음을 나타냅니다. 들어오는 스트림 이름을 클릭하고 다른 데이터 스트림을 선택하면 그래프에 있는 변환 노드의 실제 위치를 변경할 수 있습니다. 현재 변환아 해당 스트림의 모든 후속 변환과 함께 새 위치로 이동합니다.

그 후 하나 이상의 변환으로 변환을 이동하면 데이터 흐름의 새 위치가 새 분기를 통해 조인됩니다.

선택한 노드 이후에 후속 변환이 없는 경우 해당 변환만 새 위치로 이동합니다.

## <a name="hide-graph-and-show-graph"></a>그래프 숨기기 및 그래프 표시

아래쪽 구성 창의 맨 오른쪽에는 변환 구성을 작업할 때 아래쪽 창을 전체 화면으로 확장할 수 있는 단추가 있습니다. 이렇게 하면 "이전" 및 "다음" 단추를 사용하여 그래프의 구성을 탐색할 수 있습니다. 그래프 보기로 돌아가려면 아래쪽 버튼을 클릭하고 분할 화면으로 돌아갑니다.

## <a name="search-graph"></a>검색 그래프

디자인 표면의 검색 단추를 사용하여 그래프를 검색할 수 있습니다.

![검색](media/data-flow/search001.png "검색 그래프")

## <a name="next-steps"></a>다음 단계

데이터 흐름 설계를 완료한 후 디버그 버튼을 켜고 [데이터 흐름 디자이너](concepts-data-flow-debug-mode.md) 또는 파이프라인 [디버그에서](control-flow-execute-data-flow-activity.md)직접 디버그 모드에서 테스트합니다.
