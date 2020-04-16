---
title: 데이터 흐름 피벗 변환 해제 매핑
description: Azure 데이터 팩터리 매핑 데이터 흐름 언피벗 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: f42b44fdab28cb60ff903a048d9162115bf67d9c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417762"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure 데이터 팩터리 언피벗 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF 매핑 데이터 흐름에서 Unpivot를 사용하면 단일 레코드의 여러 열에서 단일 열의 여러 열에서 동일한 값을 가진 여러 레코드로 확장하여 비정규화된 데이터 집합을 보다 정규화된 버전으로 변환할 수 있습니다.

![피벗 해제 변환](media/data-flow/unpivot1.png "피벗 해제 옵션 1")

## <a name="ungroup-by"></a>그룹 해제 기준

![피벗 해제 변환](media/data-flow/unpivot5.png "피벗 해제 옵션 2")

먼저 피벗 집계에 그룹화 기준으로 사용할 열을 설정합니다. 열 목록 옆에 있는 + 기호를 사용하여 그룹 해제할 열을 하나 이상 설정합니다.

## <a name="unpivot-key"></a>피벗 해제 키

![피벗 해제 변환](media/data-flow/unpivot6.png "피벗 해제 옵션 3")

피벗 키는 ADF가 행에서 열로 피벗하는 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 열로 피벗됩니다. 그러나 열 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다.

## <a name="unpivoted-columns"></a>피벗 해제된 열

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 4")

마지막으로, 피벗된 값에 사용할 집계와 변환의 새 출력 프로젝션에 열을 표시하는 방법을 선택합니다.

(선택 사항) 행 값에서 검색된 각 새 열 이름에 추가할 접두사, 중간 및 접미사를 사용한 이름 지정 패턴을 설정할 수 있습니다.

예를 들어 “Sales”를 “Region”으로 피벗하는 경우 단순히 각 판매 값에서 새 열 값이 제공됩니다. 예를 들어 : "25", "50", "1000", ... 그러나 "Sales"의 접두사 값을 설정하면 "Sales"가 값에 접두사가 됩니다.

<img src="media/data-flow/unpivot3.png" width="400">

열 정렬을 “기본”으로 설정하면 피벗된 모든 열이 집계된 값과 함께 그룹화됩니다. 열 정렬을 “횡적”으로 설정하면 열과 값이 교대로 지정됩니다.

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 5")

피벗 해제된 최종 데이터 결과 집합은 이제 개별 행 값으로 피벗 해제된 열 합계를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

[피벗 변환을](data-flow-pivot.md) 사용하여 행을 열로 피벗합니다.
