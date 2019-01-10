---
title: Azure Time Series Insights 미리 보기의 데이터 모델링 | Microsoft Docs
description: Azure Time Series Insights 미리 보기의 데이터 모델링을 이해합니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555407"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 모델링

이 문서에서는 Azure Time Series Insights 미리 보기를 따르는 시계열 모델을 사용하는 방법을 설명합니다. 몇 가지 일반적인 데이터 시나리오를 자세히 설명합니다.

업데이트 사용 방법에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 참조하세요.

## <a name="types"></a>형식

### <a name="create-a-single-type"></a>단일 형식 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **형식**을 선택합니다. 패널을 축소하여 시계열 모델 형식에 집중합니다.

    ![Portal_one][1]

1. **추가**를 선택합니다.
1. 형식과 관련된 모든 세부 정보를 입력하고 **만들기**를 선택합니다. 이 작업은 환경에 형식을 만듭니다.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>하나 이상의 형식 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 형식 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>단일 형식 편집

형식을 선택하고 **편집**을 선택합니다. 필요에 따라 변경하고 **저장**을 선택합니다.

![Portal_four][4]

### <a name="delete-a-type"></a>형식 삭제

형식을 선택하고 **삭제**를 선택합니다. 형식과 연결된 인스턴스가 없으면 형식이 삭제됩니다.

![Portal_five][5]

## <a name="hierarchies"></a>계층 구조

### <a name="create-a-single-hierarchy"></a>단일 계층 구조 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **계층 구조**를 선택합니다. 패널을 축소하여 시계열 모델 계층 구조에 집중합니다.

    ![Portal_six][6]

1. **추가**를 선택합니다.

    ![Portal_seven][7]

1. 오른쪽 창에서 **수준 추가**를 선택합니다.

    ![Portal_eight][8]

1. 계층 구조 정보를 입력하고 **만들기**를 선택합니다.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>하나 이상의 계층 구조 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 계층 구조 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>단일 계층 구조 편집

계층 구조를 선택한 다음, **편집**을 선택합니다. 필요에 따라 변경하고 **저장**을 선택합니다.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>계층 구조 삭제

계층 구조를 선택한 다음, **삭제**를 선택합니다. 계층 구조와 연결된 인스턴스가 없으면 계층 구조가 삭제됩니다.

![Portal_twelve][12]

## <a name="instances"></a>인스턴스

### <a name="create-a-single-instance"></a>단일 인스턴스 만들기

1. 시계열 모델 선택기 패널로 이동한 다음, 메뉴에서 **인스턴스**를 선택합니다. 패널을 축소하여 시계열 모델 인스턴스에 집중합니다.

    ![Portal_thirteen][13]

1. **추가**를 선택합니다.

    ![Portal_fourteen][14]

1. 인스턴스 정보를 입력하고 형식 및 계층 구조 연결을 선택한 다음, **만들기**를 선택합니다.

### <a name="bulk-upload-one-or-more-instances"></a>하나 이상의 인스턴스 대량 업로드

1. **JSON 업로드**를 선택합니다.
1. 인스턴스 페이로드를 포함하는 파일을 선택합니다.

    ![Portal_fifteen][15]

1. **업로드**를 선택합니다.

### <a name="edit-a-single-instance"></a>단일 인스턴스 편집

인스턴스를 선택하고 **편집**을 선택합니다. 필요에 따라 변경하고 **저장**을 선택합니다.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>인스턴스 삭제

인스턴스를 선택하고 **삭제**를 선택합니다. 인스턴스와 연결된 이벤트가 없으면 인스턴스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

- 시계열 모델에 대한 자세한 내용은 [데이터 모델링](./time-series-insights-update-tsm.md)을 참조하세요.
- 미리 보기에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기 탐색기에서 데이터 시각화](./time-series-insights-update-explorer.md)를 참조하세요.
- 지원되는 JSON 셰이프에 대한 자세한 내용은 [지원되는 JSON 셰이프](./time-series-insights-send-events.md#json)를 참조하세요.

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png