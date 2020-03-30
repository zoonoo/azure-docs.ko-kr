---
title: 미리 보기 환경에서 데이터 모델링 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기에서 데이터 모델링에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470754"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 모델링

이 문서에서는 Azure 시계열 인사이트 미리 보기에서 시계열 모델을 사용하여 작업하는 방법을 설명합니다. 몇 가지 일반적인 데이터 시나리오를 자세히 설명합니다.

> [!TIP]
> * 미리 보기 [타임시리즈 모델에](time-series-insights-update-tsm.md)대해 읽어보십시오.
> * [Azure Time Series 인사이트 미리](./time-series-insights-update-explorer.md)보기 탐색기에서 미리 보기 UI 탐색에 대해 자세히 알아봅니다.

## <a name="instances"></a>인스턴스

Azure Time Series Insights 탐색기는 브라우저 내에서 인스턴스 **만들기,** **읽기,** **업데이트**및 **DELETE** 작업을 지원합니다. 

시작하려면 타임시리즈 인사이트 탐색기 **분석** 뷰에서 **모델** 뷰를 선택합니다.

### <a name="create-a-single-instance"></a>단일 인스턴스 만들기

1. 열렬 모델 선택기 패널로 이동하여 메뉴에서 **인스턴스를** 선택합니다. 선택한 타임시리즈 인사이트 환경과 연결된 모든 인스턴스가 표시됩니다.

    [![인스턴스를 먼저 선택하여 단일 인스턴스를 만듭니다.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. **+ 추가**를 선택합니다.

    [![+ 추가 단추를 선택하여 인스턴스를 추가합니다.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. 인스턴스 정보를 입력하고 형식 및 계층 구조 연결을 선택한 다음, **만들기**를 선택합니다.

### <a name="bulk-upload-one-or-more-instances"></a>하나 이상의 인스턴스 대량 업로드

> [!TIP]
> JSON의 데스크톱에 인스턴스를 저장할 수 있습니다. 그런 다음 다운로드한 JSON 파일을 다음 단계를 통해 업로드할 수 있습니다.

1. **JSON 업로드**를 선택합니다.
1. 인스턴스 페이로드를 포함하는 파일을 선택합니다.

    [![JSON을 통해 인스턴스를 일괄 업로드합니다.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. **업로드**를 선택합니다.

### <a name="edit-a-single-instance"></a>단일 인스턴스 편집

1. 인스턴스를 선택하고 **편집** 또는 **연필 아이콘을**선택합니다. 
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![단일 인스턴스를 편집합니다.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>인스턴스 삭제

1. 인스턴스를 선택하고 **삭제** 또는 **휴지통 아이콘을**선택합니다.

   [![삭제를 선택하여 인스턴스를 삭제합니다.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. **삭제를**선택하여 삭제를 확인합니다.

> [!NOTE]
> 인스턴스가 삭제되려면 필드 유효성 검사를 성공적으로 통과해야 합니다.

## <a name="hierarchies"></a>계층 구조

Azure Time Series Insights 탐색기는 브라우저 내에서 계층 **구조 CREATE,** **READ,** **UPDATE**및 **DELETE** 작업을 지원합니다. 

시작하려면 타임시리즈 인사이트 탐색기 **분석** 뷰에서 **모델** 뷰를 선택합니다.

### <a name="create-a-single-hierarchy"></a>단일 계층 구조 만들기

1. [열계 모델 선택기] 패널로 이동하여 메뉴에서 **계층을 선택합니다.** 선택한 타임시리즈 인사이트 환경과 연관된 모든 계층이 표시됩니다.

    [![창을 통해 계층을 만듭니다.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. **+ 추가**를 선택합니다.

    [![계층 구조 + 추가 단추.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. + 오른쪽 창에서 **레벨 추가를** 선택합니다.

    [![계층에 수준을 추가합니다.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. 계층 세부 정보를 입력하고 **저장을**선택합니다.

    [![계층 세부 정보를 지정합니다.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>하나 이상의 계층 구조 대량 업로드

> [!TIP]
> JSON의 바탕 화면에 계층 구조를 저장할 수 있습니다. 그런 다음 다운로드한 JSON 파일을 다음 단계를 통해 업로드할 수 있습니다.

1. **JSON 업로드**를 선택합니다.
1. 계층 구조 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    [![계층 구조의 대량 업로드를 위한 선택 입니다.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>단일 계층 구조 편집

1. 계층 구조를 선택하고 **편집** 또는 **연필 아이콘을**선택합니다.
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![단일 계층 을 편집하기 위한 선택입니다.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>계층 구조 삭제

1. 계층 구조를 선택하고 **삭제** 또는 **휴지통 아이콘을**선택합니다. 

    [![삭제 단추를 선택하여 계층을 삭제합니다.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. **삭제를**선택하여 삭제를 확인합니다.

## <a name="types"></a>유형

Azure Time Series Insights 탐색기는 브라우저 내에서 유형 **만들기,** **읽기,** **업데이트**및 **DELETE** 작업을 지원합니다. 

시작하려면 타임시리즈 인사이트 탐색기 **분석** 뷰에서 **모델** 뷰를 선택합니다.

### <a name="create-a-single-type"></a>단일 형식 만들기

1. [열등계모델 선택기] 패널로 이동하여 메뉴에서 **유형을** 선택합니다. 선택한 타임시리즈 인사이트 환경과 연관된 모든 유형이 표시됩니다.

    [![시간계 모델 유형 창입니다.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. **+ 추가를** 선택하여 새 유형 팝업 모달 **추가를 표시합니다.**
1. 형식에 대한 속성 및 변수를 입력합니다. 입력한 후 **저장을**선택합니다. 

    [![유형을 추가하는 구성 설정입니다.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>하나 이상의 형식 대량 업로드

> [!TIP]
> JSON에서 바탕 화면에 형식을 저장할 수 있습니다. 그런 다음 다운로드한 JSON 파일을 다음 단계를 통해 업로드할 수 있습니다.

1. **JSON 업로드**를 선택합니다.
1. 형식 페이로드를 포함하는 파일을 선택합니다.
1. **업로드**를 선택합니다.

    [![일괄 유형 업로드 옵션.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>단일 형식 편집

1. 유형을 선택하고 **편집** 또는 연필 **아이콘을**선택합니다.
1. 필요에 따라 변경하고 **저장**을 선택합니다.

    [![창에서 형식을 편집합니다.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>형식 삭제

1. 유형을 선택하고 **삭제** 또는 **휴지통 아이콘을**선택합니다. .

   [![삭제를 선택하여 유형을 삭제합니다.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. **삭제를**선택하여 삭제를 확인합니다.

## <a name="next-steps"></a>다음 단계

- 타임시리즈 모델에 대한 자세한 내용은 [데이터 모델링](./time-series-insights-update-tsm.md)을 참조하십시오.

- 미리 보기에 대한 자세한 내용은 [Azure Time Series Insights 미리 보기 탐색기에서 데이터 시각화](./time-series-insights-update-explorer.md)를 참조하세요.

- 지원되는 JSON 셰이프에 대해 자세히 알아보려면 [지원되는 JSON 셰이프를](./time-series-insights-send-events.md#supported-json-shapes)참조하십시오.
