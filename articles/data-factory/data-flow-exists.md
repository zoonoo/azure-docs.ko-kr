---
title: 데이터 흐름 존재 변환 Azure Data Factory 매핑
description: 데이터 팩터리를 사용 하 여 기존 행을 확인 하는 방법 Exists 변환을 사용 하 여 데이터 흐름 매핑
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387849"
---
# <a name="mapping-data-flow-exists-transformation"></a>데이터 흐름 존재 변환 매핑



있음 변환은 데이터의 행을 중지하거나 통과 허용하는 행 필터링 변환입니다. 있음 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```와 비슷합니다. Exists 변환 후 데이터 스트림의 결과 행에는 원본 1의 열 값이 원본 2에 있거나 원본 2에 없는 모든 행이 포함 됩니다.

![Exists 설정](media/data-flow/exists.png "exists 1")

Data Flow가 스트림 1의 값을 스트림 2와 비교할 수 있도록 있음의 두 번째 원본을 선택합니다.

있음 또는 없음에 대해 값을 확인하려는 원본 1과 원본 2의 열을 선택합니다.

## <a name="multiple-exists-conditions"></a>여러 개의 exists 조건

존재 하는 열 조건에 있는 각 행 옆에는 도착 행 위로 마우스를 가져가면 + 기호를 사용할 수 있습니다. 이렇게 하면 Exists 조건에 대해 여러 행을 추가할 수 있습니다. 각 추가 조건은 "And"입니다.

## <a name="custom-expression"></a>사용자 지정 식

![Exists 사용자 지정 설정](media/data-flow/exists1.png "exists 사용자 지정")

"사용자 지정 식"을 클릭 하 여 대신 존재 하거나 존재 하지 않는 조건으로 자유 형식 식을 만들 수 있습니다. 이 확인란을 선택 하면 사용자 고유의 식을 조건으로 입력할 수 있습니다.

## <a name="next-steps"></a>다음 단계

유사 변환은 [조회](data-flow-lookup.md) 및 [조인](data-flow-join.md)입니다.
