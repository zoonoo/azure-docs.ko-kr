---
title: 매핑 데이터 흐름의 여러 분기
description: 여러 분기를 사용하는 매핑 데이터 흐름에서 데이터 스트림 복제
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588754"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>매핑 데이터 흐름에서 새 분기 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

새 분기를 추가하여 동일한 데이터 스트림에 대해 여러 개의 작업 및 변환 세트를 수행할 수 있습니다. 여러 싱크에 대해 동일한 원본을 사용하거나 자체 조인 데이터를 함께 사용하려는 경우 새 분기를 추가하는 것이 유용합니다.

다른 변환과 유사하게 변환 목록에서 새 분기를 추가할 수 있습니다. 분기하려는 변환 뒤에 기존 변환이 있는 경우에만 **새 분기** 를 작업으로 사용할 수 있습니다.

![스크린샷은 다중 입력/출력 메뉴에 있는 새 분기 옵션을 보여줍니다.](media/data-flow/new-branch2.png "새 분기 추가")

아래 예제에서 데이터 흐름은 택시 여행 데이터를 읽고 있습니다. 일별 및 공급 업체별로 집계된 출력이 필요합니다. 동일한 원본에서 읽는 두 개의 개별 데이터 흐름을 만드는 대신 새 분기를 추가할 수 있습니다. 이렇게 하면 두 집계가 동일한 데이터 흐름의 일부로 실행될 수 있습니다. 

![스크린샷은 원본에서 두 개의 분기가 있는 데이터 흐름을 보여줍니다.](media/data-flow/new-branch.png "새 분기 추가")

> [!NOTE]
> 더하기(+)를 클릭하여 그래프에 변환을 추가하는 경우 후속 변환 블록이 있는 경우에만 새 분기 옵션이 표시됩니다. 이는 새 분기가 기존 스트림에 대한 참조를 생성하고 작동하기 위해 추가 업스트림 처리가 필요하기 때문입니다. 새 분기 옵션이 표시되지 않으면 파생 열 또는 다른 변환을 먼저 추가한 다음, 이전 블록으로 돌아가 새 분기를 옵션으로 표시합니다.

## <a name="next-steps"></a>다음 단계

분기 후 [데이터 흐름 변환](data-flow-transformation-overview.md)을 사용할 수 있습니다.
