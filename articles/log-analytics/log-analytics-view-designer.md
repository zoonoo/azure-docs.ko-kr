---
title: "OMS Log Analytics에서 데이터를 분석하는 보기 만들기 | Microsoft 문서"
description: "Log Analytics에서 뷰 디자이너를 사용하면 OMS 및 Azure Portal에 표시되고 OMS 리포지토리에 있는 데이터를 여러 방법으로 시각화하는 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>뷰 디자이너를 사용하여 Log Analytics에서 사용자 지정 보기 만들기
[Log Analytics](log-analytics-overview.md)에서 뷰 디자이너를 사용하면 OMS 리포지토리에 있는 데이터를 여러 방법으로 시각화하는 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다.

뷰 디자이너에 적용할 수 있는 다른 문서는 다음과 같습니다.

* [타일 참조](log-analytics-view-designer-tiles.md) - 사용자 지정 보기에 사용할 수 있는 타일 각각의 설정에 대한 참조
* [시각화 요소 참조](log-analytics-view-designer-parts.md) - 사용자 지정 보기에 사용할 수 있는 타일 각각의 설정에 대한 참조

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우 모든 뷰의 쿼리를 [새 쿼리 언어](https://go.microsoft.com/fwlink/?linkid=856078)로 써야 합니다.  작업 영역을 업그레이드하기 전에 생성된 모든 뷰는 자동으로 변환됩니다.

## <a name="concepts"></a>개념
뷰 디자이너에서 만드는 보기에 포함되는 요소는 다음 표와 같습니다.

| 부 | 설명 |
|:--- |:--- |
| 타일 |기본 Log Analytics 개요 대시보드에서 표시됩니다.  사용자 지정 보기에 있는 정보를 요약하는 시각적 개체를 포함하고 있습니다.  타일 유형마다 OMS 리포지토리에 있는 레코드에 대해 차별화된 시각화를 제공합니다.  타일을 클릭하면 사용자 지정 보기가 열립니다. |
| 사용자 지정 보기 |사용자가 타일을 클릭할 때 표시됩니다.  시각화 요소를 하나 이상 포함하고 있습니다. |
| 시각화 요소 |OMS 리포지토리의 데이터에 대한 [로그 검색](log-analytics-log-searches.md) 하나 이상에 기반한 시각화입니다.  대부분의 요소에는 높은 수준의 시각화와 상위 결과 목록을 제공하는 머리글이 포함되어 있습니다.  요소 유형마다 OMS 리포지토리에 있는 레코드에 대해 차별화된 시각화를 제공합니다.  요소의 항목을 클릭하면 상세 레코드를 제공하는 로그 검색을 수행합니다. |

![뷰 디자이너 개요](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>작업 영역에 뷰 디자이너 추가
뷰 디자이너가 미리 보기 상태에 있는 동안에 OMS 포털의 **설정** 섹션에서 **미리 보기 기능**을 선택하여 작업 영역에 뷰 디자이너를 추가해야 합니다.

![미리 보기 사용](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>보기 만들기 및 편집
### <a name="create-a-new-view"></a>새 보기 만들기
기본 OMS 대시보드에서 뷰 디자이너 타일을 클릭하여 **뷰 디자이너**에서 새 보기를 엽니다.

![뷰 디자이너 타일](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>기존 보기 편집
뷰 디자이너에서 기존 보기를 편집하려면 기본 OMS 대시보드에서 해당 타일을 클릭하여 해당 보기를 엽니다.  그런 다음 **편집** 단추를 클릭하여 뷰 디자이너에서 해당 보기를 엽니다.

![보기 편집](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>기존 보기 복제
보기를 복제하면 새 보기를 만들고 뷰 디자이너에서 이 보기를 엽니다.  새 보기에는 원본과 동일한 이름 끝에 "복사본"이 추가된 이름이 적용됩니다.  보기를 복제하려면 기본 OMS 대시보드에서 해당 타일을 클릭하여 기존 보기를 엽니다.  그런 다음 **복제** 단추를 클릭하여 뷰 디자이너에서 해당 보기를 엽니다.

![보기 복제](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>기존 보기 삭제
기존 보기를 삭제하려면 기본 OMS 대시보드에서 해당 타일을 클릭하여 해당 보기를 엽니다.  그런 다음 **편집** 단추를 클릭하여 뷰 디자이너에서 해당 보기를 열고 **보기 삭제**를 클릭합니다.

![보기 삭제](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>기존 보기 내보내기
보기는 다른 작업 영역으로 가져오거나 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)에서 사용할 수 있는 JSON 파일에 내보낼 수 있습니다.  기존 보기를 내보려면 기본 OMS 대시보드에서 해당 타일을 클릭하여 해당 보기를 엽니다.  그런 다음 **내보내기** 단추를 클릭하여 브라우저의 다운로드 폴더에 파일을 만듭니다.  파일 이름은 보기 이름과 *omsview* 확장명으로 구성됩니다.

![보기 내보내기](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>기존 보기 가져오기
다른 관리 그룹에서 내보낸 *omsview* 파일을 가져올 수 있습니다.  기존 보기를 가져오려면 먼저 새 보기를 만듭니다.  그런 다음 **가져오기** 단추를 클릭하여 *omsview* 파일을 선택합니다.  파일의 구성이 기존 보기에 복사됩니다.

![보기 내보내기](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>뷰 디자이너 작업
뷰 디자이너에는 세 개의 창이 있습니다.  **디자인** 창에서는 사용자 지정 보기를 표시합니다.  **제어** 창에서 **디자인** 창으로 타일과 요소를 추가하면 해당 타일과 요소가 보기에 추가됩니다.  **속성** 창에서는 타일 또는 선택한 요소의 속성을 표시합니다.

![뷰 디자이너](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>보기 타일 구성
사용자 지정 보기에는 타일 하나만 있을 수 있습니다.  **제어** 창에서 **타일** 탭을 선택하여 현재 타일을 확인하거나 다른 타일을 선택합니다.  **속성** 창에서는 현재 타일의 속성을 표시합니다.  [타일 참조](log-analytics-view-designer-tiles.md)의 세부 정보에 따라 타일 속성을 구성한 다음 **적용**을 클릭하여 변경 내용을 저장합니다.

### <a name="configure-visualization-parts"></a>시각화 요소 구성
보기에는 시각화 요소가 얼마든지 포함될 수 있습니다.  **보기** 탭, 보기에 추가할 시각화 요소를 차례로 선택합니다.  **속성** 창에서는 선택한 요소의 속성을 표시합니다.  [시각화 요소 참조](log-analytics-view-designer-parts.md)의 세부 정보에 따라 보기일 속성을 구성한 다음 **적용**을 클릭하여 변경 내용을 저장합니다.

### <a name="delete-a-visualization-part"></a>시각화 요소 삭제
요소 오른쪽 위 모서리의 **X** 단추를 클릭하면 보기에서 시각화 요소를 제거할 수 있습니다.

### <a name="rearrange-visualization-parts"></a>시각화 요소 다시 정렬
보기에는 시각화 요소의 행 하나만 있습니다.  기존 요소를 클릭하여 새 위치로 끌어가면 보기에서 해당 요소를 다시 정렬할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 [타일](log-analytics-view-designer-tiles.md) 추가
* 사용자 지정 보기에 [시각화 요소](log-analytics-view-designer-parts.md) 추가
