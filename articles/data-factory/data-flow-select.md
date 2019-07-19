---
title: Azure Data Factory Mapping Data Flow 선택 변환
description: Azure Data Factory Mapping Data Flow 선택 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314208"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory Mapping Data Flow 선택 변환
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

열 선택도 (열 수 줄이기), 별칭 열 및 스트림 이름 및 열 다시 정렬에 대해이 변환을 사용 합니다.

## <a name="how-to-use-select-transformation"></a>Select 변환을 사용 하는 방법
선택 변환을 사용하면 전체 스트림이나 해당 스트림의 열에 별칭을 지정하고, 다른 이름(별칭)을 할당한 다음, 데이터 흐름의 뒷부분에서 새 이름을 참조할 수 있습니다. 이 변환은 셀프 조인 시나리오에 유용합니다. ADF Data Flow에서 셀프 조인을 구현하는 방법은 스트림을 사용하고, “새 분기”로 분기한 다음, 바로 뒤에 “선택” 변환을 추가하는 것입니다. 이제 해당 스트림에 새 이름이 지정되며, 이 이름을 사용하여 원래 스트림에 다시 조인함으로써 셀프 조인을 만들 수 있습니다.

![셀프 조인](media/data-flow/selfjoin.png "셀프 조인")

위의 다이어그램에서 선택 변환은 맨 위에 있습니다. 이 변환은 원래 스트림의 별칭을 “OrigSourceBatting”으로 지정합니다. 아래 강조 표시 된 조인 변환에서이 Select alias stream을 오른쪽 조인으로 사용 하 여 내부 조인의 왼쪽 & 오른쪽에서 동일한 키를 참조할 수 있습니다.

데이터 흐름에서 열을 선택 취소하는 방법으로 선택을 사용할 수도 있습니다. 예를 들어 싱크에 6개 열이 정의되어 있지만 변환할 특정 열 3개만 선택하고 싱크로 진행하는 경우 선택 변환을 사용하여 해당 3개만 선택할 수 있습니다.

> [!NOTE]
> 특정 열만 선택하려면 “모두 선택”을 꺼야 합니다.

![선택 변환](media/data-flow/select001.png "선택 별칭")

## <a name="options"></a>변수
* “선택”의 기본 설정은 모든 수신 열을 포함하고 원래 이름을 유지하는 것입니다. 선택 변환의 이름을 설정하여 스트림의 별칭을 지정할 수 있습니다.
* 개별 열의 별칭을 지정하려면 “모두 선택”을 선택 취소하고 맨 아래에 있는 열 매핑을 사용합니다.
* 입력 또는 출력 메타 데이터에서 중복 열을 제거 하려면 중복 건너뛰기를 선택 합니다.

![중복 항목 건너뛰기](media/data-flow/select-skip-dup.png "중복 항목 건너뛰기")

## <a name="next-steps"></a>다음 단계
* Select를 사용 하 여 이름을 바꾸고, 순서를 바꾸고, 별칭을 사용 하 여 데이터를 데이터 저장소로 이동 하려면 [싱크 변환을](data-flow-sink.md) 사용 합니다.
