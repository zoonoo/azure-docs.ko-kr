---
title: Azure Data Factory 데이터 흐름 이동 노드
description: 에 Azure 데이터 팩터리 매핑 데이터 흐름 다이어그램 노드를 이동 하는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519964"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapping Data Flow 노드 이동

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![집계 변환 옵션](media/data-flow/agghead.png "집계 헤더")

Azure Data Factory 데이터 흐름 디자인 화면은 데이터 흐름을 위에서 아래로, 왼쪽에서 오른쪽으로 빌드할 수 있는 “생성” 화면입니다. 더하기(+) 기호를 사용하여 각 변환에 연결된 도구 상자가 있습니다. 자유 형식 DAG 환경에서 가장자리를 통해 노드에 연결하는 대신 비즈니스 논리에 집중합니다.

따라서 끌어서 놓기 패러다임을 사용하지 않고 변환 노드를 “이동”하는 방법은 들어오는 스트림을 변경하는 것입니다. 대신 “들어오는 스트림”변경하여 변환을 이동하게 됩니다.

## <a name="streams-of-data-inside-of-data-flow"></a>데이터 흐름 내에서 데이터 스트림

Azure Data Factory 데이터 흐름에서 스트림은 데이터의 흐름을 나타냅니다. 변환 설정 창에 “들어오는 스트림” 필드가 표시됩니다. 이는 들어오는 데이터 스트림이 해당 변환에 공급되었음을 나타냅니다. 들어오는 스트림 이름을 클릭하고 다른 데이터 스트림을 선택하면 그래프에 있는 변환 노드의 실제 위치를 변경할 수 있습니다. 현재 변환아 해당 스트림의 모든 후속 변환과 함께 새 위치로 이동합니다.

그 후 하나 이상의 변환으로 변환을 이동하면 데이터 흐름의 새 위치가 새 분기를 통해 조인됩니다.

선택한 노드 이후에 후속 변환이 없는 경우 해당 변환만 새 위치로 이동합니다.

## <a name="next-steps"></a>다음 단계

데이터 흐름 디자인을 완료 한 후 디버그 단추를 설정 하 고 테스트할 아웃 디버그 모드 중 하나에서 직접 합니다 [데이터 흐름 디자이너](concepts-data-flow-debug-mode.md) 하거나 [파이프라인 디버그](control-flow-execute-data-flow-activity.md)합니다.
