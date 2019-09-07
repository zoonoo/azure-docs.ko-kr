---
title: Azure Time Series Insights 미리 보기의 데이터 모델링 | Microsoft Docs
description: Azure Time Series Insights 미리 보기의 데이터 모델링을 이해합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 245a69f5e5834e68bbbd17a96859a93bc16eacbe
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744124"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 모델링

이 문서에서는 Azure Time Series Insights 미리 보기를 따르는 시계열 모델을 사용하는 방법을 설명합니다. 몇 가지 일반적인 데이터 시나리오를 자세히 설명합니다.

업데이트 사용 방법에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 참조하세요.

## <a name="types"></a>유형

### <a name="create-a-single-type"></a>단일 형식 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **형식**을 선택합니다. 패널을 축소하여 시계열 모델 형식에 집중합니다.

    [![단일 형식 만들기](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. 선택 **+ 추가**합니다.
1. 형식과 관련된 모든 세부 정보를 입력하고 **만들기**를 선택합니다. 이 작업은 환경에 형식을 만듭니다.

    [![형식 추가](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>하나 이상의 형식 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 형식 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    [![JSON 업로드](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>단일 형식 편집

1. 형식을 선택하고 **편집**을 선택합니다. 
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![형식 편집](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>형식 삭제

1. 형식을 선택하고 **삭제**를 선택합니다.
1. 형식과 연결된 인스턴스가 없으면 형식이 삭제됩니다.

    [![유형 삭제](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>계층 구조

### <a name="create-a-single-hierarchy"></a>단일 계층 구조 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **계층 구조**를 선택합니다. 패널을 축소하여 시계열 모델 계층 구조에 집중합니다.

    [![계층 선택](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. 선택 **+ 추가**합니다.

    [![계층 추가](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. 오른쪽 창에서 **+ 수준 추가** 를 선택 합니다.

    [![수준 추가](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. 계층 구조 정보를 입력하고 **만들기**를 선택합니다.

    [![수준 만들기](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>하나 이상의 계층 구조 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 계층 구조 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    [![계층 대량 업로드](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>단일 계층 구조 편집

1. 계층 구조를 선택한 다음, **편집**을 선택합니다.
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![단일 계층 편집](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>계층 구조 삭제

1. 계층 구조를 선택한 다음, **삭제**를 선택합니다. 
1. 계층 구조와 연결된 인스턴스가 없으면 계층 구조가 삭제됩니다.

    [![계층 삭제](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>인스턴스

### <a name="create-a-single-instance"></a>단일 인스턴스 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **인스턴스**를 선택합니다. 패널을 축소하여 시계열 모델 인스턴스에 집중합니다.

    [![단일 인스턴스 만들기](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. **추가**를 선택합니다.

    [![인스턴스 추가](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. 인스턴스 정보를 입력하고 형식 및 계층 구조 연결을 선택한 다음, **만들기**를 선택합니다.

### <a name="bulk-upload-one-or-more-instances"></a>하나 이상의 인스턴스 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 인스턴스 페이로드를 포함하는 파일을 선택합니다.

    [![하나 이상의 인스턴스 대량 업로드](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. **업로드**를 선택합니다.

### <a name="edit-a-single-instance"></a>단일 인스턴스 편집

1. 인스턴스를 선택하고 **편집**을 선택합니다. 
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![단일 인스턴스 편집](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>다음 단계

- 시계열 모델에 대한 자세한 내용은 [데이터 모델링](./time-series-insights-update-tsm.md)을 참조하세요.

- 미리 보기에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기 탐색기에서 데이터 시각화](./time-series-insights-update-explorer.md)를 참조하세요.

- 지원되는 JSON 셰이프에 대한 자세한 내용은 [지원되는 JSON 셰이프](./time-series-insights-send-events.md#json)를 참조하세요.
