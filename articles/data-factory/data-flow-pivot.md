---
title: Azure Data Factory Mapping Data Flow 피벗 변환
description: Azure Data Factory Mapping Data Flow 피벗 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569895"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure Data Factory Mapping Data Flow 피벗 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ADF Data Flow의 피벗을 하나 이상의 그룹화 열의 고유한 행 값이 개별 열로 변환되는 집계로 사용합니다. 기본적으로, 행 값을 새 열로 피벗할 수 있습니다(데이터를 메타데이터로 변환).

![피벗 옵션](media/data-flow/pivot1.png "피벗 1")

## <a name="group-by"></a>그룹화 기준

![피벗 옵션](media/data-flow/pivot2.png "피벗 2")

먼저 피벗 집계에 그룹화 기준으로 사용할 열을 설정합니다. 여기에서 열 목록 옆에 +기호를 사용하여 둘 이상의 열을 설정할 수 있습니다.

## <a name="pivot-key"></a>피벗 키

![피벗 옵션](media/data-flow/pivot3.png "피벗 3")

피벗 키는 ADF가 행에서 열로 피벗하는 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 열로 피벗됩니다. 그러나 열 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다.

## <a name="pivoted-columns"></a>피벗 열

![피벗 옵션](media/data-flow/pivot4.png "피벗 4")

마지막으로, 피벗된 값에 사용할 집계와 변환의 새 출력 프로젝션에 열을 표시하는 방법을 선택합니다.

(선택 사항) 행 값에서 검색된 각각의 새 열 이름에 추가할 접두사, 중간 및 접미사를 사용한 이름 지정 패턴을 설정할 수 있습니다.

예를 들어 “Sales”를 “Region”으로 피벗하는 경우 단순히 각 판매 값의 새 열 값(예: "25", "50", "1000" 등)을 얻게 됩니다. 그러나 접두사 값 "Sales-"로 설정 하면 각 열 값에 추가 됩니다 "Sales-" 값의 시작.

![피벗 옵션](media/data-flow/pivot5.png "피벗 5")

열 정렬을 “기본”으로 설정하면 피벗된 모든 열이 집계된 값과 함께 그룹화됩니다. 열 정렬을 “횡적”으로 설정하면 열과 값이 교대로 지정됩니다.

### <a name="aggregation"></a>집계

피벗 값에 사용하려는 집계를 설정하려면 피벗 열 창 아래쪽의 필드를 클릭합니다. 집계 식을 작성하고 집계된 새 값에 대해 설명이 포함된 별칭 이름을 제공할 수 있는 ADF Data Flow 식 작성기를 시작합니다.

ADF Data Flow 식 언어를 사용하여 식 작성기의 피벗 열 변환을 설명합니다. https://aka.ms/dataflowexpressions

### <a name="how-to-rejoin-original-fields"></a>원본 필드를 다시 조인하는 방법
> [!NOTE]
> 피벗 변환은 집계, 그룹화 및 피벗 작업에 사용되는 열만 프로젝션합니다. 흐름의 이전 단계에서 다른 열을 포함 하려는 경우 이전 단계에서 새 분기를 사용 하 고 자체 조인 패턴을 사용 하 여 원래 메타 데이터를 사용 하 여 흐름을 연결 합니다.

## <a name="next-steps"></a>다음 단계

시도 된 [피벗 해제 변환](data-flow-unpivot.md) 행 값으로 열 값을 설정 합니다. 
