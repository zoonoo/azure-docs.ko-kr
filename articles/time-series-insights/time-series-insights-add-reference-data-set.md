---
title: Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경에서 참조 데이터 집합을 추가하여 데이터를 확장하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec18
ms.openlocfilehash: 62c3b35f80300a38213b089567c3f63a8a2ce8db
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235064"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 참조 데이터 집합 만들기

이 문서에서는 Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법을 설명합니다. 참조 데이터는 값을 확장하기 위해 원본 데이터에 연결하는 데 유용합니다.

참조 데이터 집합은 이벤트 원본의 이벤트로 확장된 항목의 컬렉션입니다. Time Series Insights 수신 엔진은 이벤트 원본의 각 이벤트와 참조 데이터 집합의 해당 데이터 행을 조인합니다. 이렇게 보강된 이벤트는 쿼리에 사용할 수 있습니다. 이 조인은 참조 데이터 집합에서 정의된 기본 키 열을 기준으로 합니다.

참조 데이터는 소급되어 조인되지 않습니다. 따라서만 현재 및 미래의 수신 데이터 일치 되 고 구성 및 업로드 된 후에 참조 데이터 집합에 가입 합니다.

## <a name="video"></a>비디오

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Time Series Insight의 참조 데이터 모델에 알아봅니다.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>참조 데이터 집합 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기존 Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 클릭합니다. Time Series Insights 환경을 선택합니다.

1. **개요** 페이지를 선택합니다. **Time Series Insights 탐색기 URL**을 찾은 후 링크를 엽니다.  

   TSI 환경에 대한 탐색기 보기

1. TSI 탐색기에서 환경 선택기를 확장합니다. 활성 환경을 선택합니다. 탐색기 페이지에서 오른쪽 상단에 있는 참조 데이터 아이콘을 선택합니다.

   [![참조 데이터 추가](media/add-reference-data-set/add_reference_data.png)](media/add-reference-data-set/add_reference_data.png#lightbox)

1. **+ 데이터 집합 추가** 단추를 선택하여 새 데이터 집합 추가를 시작합니다.

   [![데이터 집합 추가](media/add-reference-data-set/add_data_set.png)](media/add-reference-data-set/add_data_set.png#lightbox)

1. **새 참조 데이터 집합** 페이지에서 데이터의 형식을 선택합니다.
   - 쉼표로 구분된 데이터에 대해 **CSV**를 선택합니다. 첫 번째 행은 머리글 행으로 처리됩니다.
   - JSON(javascript 개체 표기법) 형식 데이터에 대해 **JSON 배열**을 선택합니다.

   [![데이터 형식을 선택 합니다.](media/add-reference-data-set/add_data.png)](media/add-reference-data-set/add_data.png#lightbox)

1. 다음 두 가지 방법 중 하나를 사용하여 데이터를 제공합니다.
   - 데이터를 텍스트 편집기에 붙여 넣습니다. 그런 다음, **참조 데이터 구문 분석** 단추를 선택합니다.
   - **파일 선택** 단추를 선택하여 로컬 텍스트 파일의 데이터를 추가합니다.

   예를 들어 [![붙여넣은 CSV 데이터](media/add-reference-data-set/csv_data_pasted.png)](media/add-reference-data-set/csv_data_pasted.png#lightbox)

   예를 들어 [![JSON 데이터를 붙여 넣습니다.](media/add-reference-data-set/json_data_pasted.png)](media/add-reference-data-set/json_data_pasted.png#lightbox)

   데이터 값을 구문 분석하는 동안 오류가 발생하면 페이지 하단에 빨간색으로 오류가 표시됩니다(예: `CSV parsing error, no rows extracted`).

1. 데이터가 구문 분석되면 데이터 그리드에 데이터를 나타내는 열 및 행이 표시됩니다.  데이터 그리드가 정확한지 검토합니다.

   [![참조 데이터 추가](media/add-reference-data-set/parse_data.png)](media/add-reference-data-set/parse_data.png#lightbox)

1. 각 열을 검토하여 예상되는 데이터 형식을 확인한 후, 필요에 따라 데이터 형식을 변경합니다.  열 머리글에서 데이터 형식 기호를 선택합니다. 예를 들어 double(숫자 데이터)의 경우 **#** 을 선택하고, 부울의 경우 **T|F**를 선택하고, 문자열의 경우 **Abc**를 선택합니다.

   [![열 머리글에 대 한 데이터 형식을 선택 합니다.](media/add-reference-data-set/choose_datatypes.png)](media/add-reference-data-set/choose_datatypes.png#lightbox)

1. 필요한 경우 열 머리글의 이름을 바꿉니다. 해당 속성을 이벤트 원본에 조인하려면 키 열 이름이 필요합니다. 참조 데이터 키 열 이름이 들어오는 데이터의 이벤트 이름과 정확하게(대/소문자 포함) 일치하는지 확인합니다. 키가 아닌 열 이름은 해당 참조 데이터 값을 사용하여 들어오는 데이터를 확장하는 데 사용됩니다.

1. 필요에 따라 더 많은 참조 데이터 값을 추가하려면 **행 추가** 또는 **열 추가**를 클릭합니다.

1. 필요에 따라 특정 행을 검토하려면 **행 필터링...** 필드에 값을 입력합니다. 필터는 데이터를 검토하는 데 유용하지만 데이터를 업로드할 때는 적용되지 않습니다.

1. 데이터 그리드 위의 **데이터 집합 이름** 필드에 입력하여 데이터 집합 이름을 지정합니다.

    [![데이터 집합 이름을 지정 합니다.](media/add-reference-data-set/name_reference_dataset.png)](media/add-reference-data-set/name_reference_dataset.png#lightbox)

1. 데이터 그리드 위의 드롭다운을 선택하여 데이터 집합에 **기본 키** 열을 제공합니다.

    [![키 열을 선택 합니다.](media/add-reference-data-set/set_primary_key.png)](media/add-reference-data-set/set_primary_key.png#lightbox)

    필요에 따라 보조 키 열을 복합 기본 키로 추가하려면 **+** 단추를 선택합니다. 선택을 실행 취소해야 할 경우 드롭다운 목록에서 빈 값을 선택하여 보조 키를 제거합니다.

1. 데이터를 업로드하려면 **행 업로드** 단추를 선택합니다.

    [![업로드](media/add-reference-data-set/upload_rows.png)](media/add-reference-data-set/upload_rows.png#lightbox)

    이 페이지에서는 완료된 업로드를 확인하고 **데이터 세트가 업로드됨** 메시지를 표시합니다.

## <a name="next-steps"></a>다음 단계

* 프로그래밍 방식으로 [참조 데이터를 관리](time-series-insights-manage-reference-data-csharp.md)합니다.

* 전체 API 참조는 [참조 데이터 API](/rest/api/time-series-insights/ga-reference-data-api) 문서를 참조하세요.
