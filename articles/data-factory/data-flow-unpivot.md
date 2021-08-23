---
title: 매핑 데이터 흐름의 피벗 해제 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 매핑 데이터 흐름 피벗 해제 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 07/14/2020
ms.openlocfilehash: 5313af13d02df1ab33764bf268edd601005cb8af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642726"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 피벗 해제 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ADF 매핑 데이터 흐름의 피벗 해제는 단일 레코드에 있는 여러 열의 값을 단일 열의 값이 동일한 여러 레코드로 확장하여 비정규화된 데이터 세트를 보다 정규화된 버전으로 전환하는 방법으로 사용합니다.

![메뉴에서 선택한 내보내기 작업을 보여 주는 스크린샷](media/data-flow/unpivot1.png "피벗 해제 옵션 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>그룹 해제 기준

![그룹 해제 기준 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷](media/data-flow/unpivot5.png "피벗 해제 옵션 2")

먼저 피벗 해제 집계에 대해 그룹을 해제하려는 열을 설정합니다. 열 목록 옆에 있는 + 기호를 사용하여 그룹 해제할 열을 하나 이상 설정합니다.

## <a name="unpivot-key"></a>피벗 해제 키

![피벗 해제 키 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷](media/data-flow/unpivot6.png "피벗 해제 옵션 3")

피벗 해제 키는 ADF가 열에서 행으로 피벗할 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 행으로 피벗됩니다. 그러나 행 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다.

## <a name="unpivoted-columns"></a>피벗 해제된 열

![데이터 미리 보기 탭이 선택된 피벗 해제 설정을 보여 주는 스크린샷](media/data-flow//unpivot7.png "피벗 해제 옵션 4")

마지막으로, 행으로 변환된 피벗 해제 열의 값을 저장하기 위한 열 이름을 선택합니다.

(선택 사항) Null 값이 있는 행을 삭제할 수 있습니다.

예를 들어, SumCost는 위에 공유된 예제에서 선택한 열 이름입니다.

![Fruit 열을 피벗 해제 키로 사용하여 피벗 해제 변환 전후에 PO, Vendor, Fruit 열을 보여 주는 이미지](media/data-flow/unpivot3.png)

열 정렬을 “기본”으로 설정하면 단일 값에서 새 피벗 해제된 열이 모두 그룹화됩니다. 열 정렬을 “수평”으로 설정하면 기존 열에서 생성된 새 피벗 해제된 열이 그룹화됩니다.

![변환의 결과를 보여 주는 스크린샷](media/data-flow//unpivot7.png "피벗 해제 옵션 5")

피벗 해제된 최종 데이터 결과 집합은 이제 개별 행 값으로 피벗 해제된 열 합계를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

[피벗 변환](data-flow-pivot.md)을 사용하여 행을 열로 피벗합니다.
