---
title: Azure Data Factory Mapping Data Flow 새 분기 변환
description: Azure Data Factory Mapping Data Flow 새 분기 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271555"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory Mapping Data Flow 새 분기 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![분기 옵션](media/data-flow/menu.png "메뉴")

분기는 데이터 흐름의 현재 데이터 스트림을 사용하고 다른 스트림에 복제합니다. 새 분기를 사용하여 동일한 데이터 스트림에 대해 여러 개의 작업 및 변환 세트를 수행할 수 있습니다.

예제: 데이터 흐름에 선택한 열 세트 및 데이터 형식 변환을 사용한 원본 변환이 있습니다. 해당 원본 바로 뒤에 파생 열을 배치합니다. 파생 열에서 이름과 성을 결합하여 새로운 “전체 이름” 필드를 만드는 새 필드를 만들었습니다.

한 행에서 변환 세트와 싱크를 사용하여 새 스트림을 처리하고, 새 분기를 사용하여 해당 스트림의 복사본을 만든 다음, 다른 변환 세트로 동일한 데이터를 변환할 수 있습니다. 복사된 데이터를 별도의 분기로 변환하면 이후에 해당 데이터를 별도의 위치로 싱크할 수 있습니다.

> [!NOTE]
> “새 분기”는 분기하려는 현재 위치 뒤에 후속 변환이 있는 경우에만 + 변환 메뉴에 동작으로 표시됩니다. 즉, 선택 후 다른 변환을 추가할 때까지는 “새 분기” 옵션이 끝에 표시되지 않습니다.

![분기](media/data-flow/branch2.png "분기 2")
