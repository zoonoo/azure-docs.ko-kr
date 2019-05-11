---
title: Azure 데이터 팩터리 매핑 흐름 데이터가 변환
description: Exists 변환이 포함 된 흐름 매핑 data factory를 사용 하 여 기존 행에 대 한 확인 하는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235971"
---
# <a name="mapping-data-flow-exists-transformation"></a>변환에 있는 데이터 흐름 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

있음 변환은 데이터의 행을 중지하거나 통과 허용하는 행 필터링 변환입니다. 있음 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```와 비슷합니다. 데이터 스트림에서 결과 행은 존재 변환 후 원본 1의에서 열 값 2 원본에 있는 모든 행을 포함 하거나 됩니다 또는 원본 2에에서 존재 하지 않습니다.

![있음 설정](media/data-flow/exists.png "있음 1")

Data Flow가 스트림 1의 값을 스트림 2와 비교할 수 있도록 있음의 두 번째 원본을 선택합니다.

있음 또는 없음에 대해 값을 확인하려는 원본 1과 원본 2의 열을 선택합니다.

## <a name="multiple-exists-conditions"></a>여러 조건을 존재합니다

Exists에 대 한 열 조건에서 각 행에 옆에 있는 확인할 수는 + 도달 행을 마우스로 가리킬 때 기호를 사용할 수 있습니다. 이렇게 하면 Exists 조건에 대 한 여러 행을 추가할 수 있습니다. 각 추가 조건은 "및".

## <a name="custom-expression"></a>사용자 지정 식

![사용자 지정 설정을 존재](media/data-flow/exists1.png "있는 사용자 지정")

대신으로 자유 형식 식을 만들려면 "사용자 지정 식"을 클릭할 수에 존재 하지 않거나 조건이 not-존재 합니다. 이 확인란을 선택 하면 사용자 고유의 식을 조건으로 입력할 수 있습니다.

## <a name="next-steps"></a>다음 단계

변환은 유사한 [조회](data-flow-lookup.md) 하 고 [조인](data-flow-join.md)합니다.
