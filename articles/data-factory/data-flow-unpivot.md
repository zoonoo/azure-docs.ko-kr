---
title: 매핑 데이터 흐름의 피벗 해제 변환
description: 데이터 흐름 피벗 해제 변환 Azure Data Factory 매핑
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ea8881adf39a315df7746dbce14dedcbee18ccf6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521053"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 피벗 해제 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

단일 레코드의 여러 열 값을 단일 열에서 동일한 값을 가진 여러 레코드로 확장 하 여 정규화 되지 않은 데이터 집합을 보다 정규화 된 버전으로 전환 하 여 정규화 되지 않은 데이터 집합을 보다 정규화 된 버전으로 전환 하는 방법으로 ADF 매핑 데이터 흐름에서 Unpivot

![피벗 해제 변환](media/data-flow/unpivot1.png "피벗 해제 옵션 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>그룹 해제 기준

![피벗 해제 변환](media/data-flow/unpivot5.png "피벗 해제 옵션 2")

먼저 피벗 해제 집계에 대해 그룹화를 해제 하려는 열을 설정 합니다. 열 목록 옆에 있는 + 기호를 사용하여 그룹 해제할 열을 하나 이상 설정합니다.

## <a name="unpivot-key"></a>피벗 해제 키

![피벗 해제 변환](media/data-flow/unpivot6.png "피벗 해제 옵션 3")

피벗 해제 키는 ADF가 열에서 행으로 피벗 하는 열입니다. 기본적으로이 필드에 대 한 데이터 집합의 각 고유 값은 행으로 피벗 됩니다. 그러나 필요에 따라 행 값에 대해 피벗 하려는 데이터 집합의 값을 입력할 수 있습니다.

## <a name="unpivoted-columns"></a>피벗 해제된 열

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 4")

마지막으로 행으로 변환 된 피벗 해제 열에 대 한 값을 저장 하기 위한 열 이름을 선택 합니다.

필드 Null 값이 있는 행을 삭제할 수 있습니다.

예를 들어 SumCost는 위에서 공유한 예제에서 선택한 열 이름입니다.

![과일 열을 unipivot 키로 사용 하 여 unipivot 변환 전후에 PO, 공급 업체 및 과일 열을 표시 하는 이미지입니다.](media/data-flow/unpivot3.png)

열 정렬을 "Normal"로 설정 하면 단일 값에서 모든 새 피벗 해제 열을 함께 그룹화 합니다. 열 정렬을 "방향"으로 설정 하면 기존 열에서 생성 된 새 피벗 해제 열이 함께 그룹화 됩니다.

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 5")

피벗 해제된 최종 데이터 결과 집합은 이제 개별 행 값으로 피벗 해제된 열 합계를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

[피벗 변환을](data-flow-pivot.md) 사용 하 여 행을 열로 피벗 합니다.
