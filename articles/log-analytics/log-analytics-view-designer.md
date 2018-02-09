---
title: "Azure Log Analytics에서 데이터를 분석하는 보기 만들기 | Microsoft Docs"
description: "Log Analytics에서 뷰 디자이너를 사용하면 Azure Portal에 표시되고 Log Analytics 작업 공간에 있는 데이터를 여러 방법으로 시각화하는 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다."
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
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>뷰 디자이너를 사용하여 Log Analytics에서 사용자 지정 보기 만들기
[Log Analytics](log-analytics-overview.md)에서 뷰 디자이너를 사용하면 Azure Portal에서 Log Analytics 작업 영역에 있는 여러 데이터 시각화가 포함된 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다.

뷰 디자이너에 적용할 수 있는 다른 문서는 다음과 같습니다.

* [타일 참조](log-analytics-view-designer-tiles.md) - 사용자 지정 보기에 사용할 수 있는 타일 각각의 설정에 대한 참조
* [시각화 요소 참조](log-analytics-view-designer-parts.md) - 사용자 지정 보기에 사용할 수 있는 타일 각각의 설정에 대한 참조

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우 모든 뷰의 쿼리를 [새 쿼리 언어](https://go.microsoft.com/fwlink/?linkid=856078)로 써야 합니다.  작업 영역을 업그레이드하기 전에 생성된 모든 뷰는 자동으로 변환됩니다.

## <a name="concepts"></a>개념
보기는 Azure Portal에서 Log Analytics 작업 영역의 **개요** 페이지에 표시됩니다.  각 사용자 지정 보기에 대한 타일은 해당 솔루션에 대한 타일이 동일한 작업 영역에 설치되어 사전순으로 표시됩니다.

![개요 페이지](media/log-analytics-view-designer/overview-page.png)

뷰 디자이너에서 만드는 보기에 포함되는 요소는 다음 표와 같습니다.

| 부 | 설명 |
|:--- |:--- |
| 타일 |Log Analytics 작업 영역에 대한 개요 페이지에 표시됩니다.  사용자 지정 보기에 있는 정보를 요약하는 시각적 개체를 포함하고 있습니다.  타일 유형마다 레코드에 대해 차별화된 시각화를 제공합니다.  타일을 클릭하면 사용자 지정 보기가 열립니다. |
| 사용자 지정 보기 |사용자가 타일을 클릭할 때 표시됩니다.  시각화 요소를 하나 이상 포함하고 있습니다. |
| 시각화 요소 |Log Analytics 작업 영역의 데이터에 대한 [로그 검색](log-analytics-log-searches.md) 하나 이상에 기반한 시각화입니다.  대부분의 요소에는 높은 수준의 시각화와 상위 결과 목록을 제공하는 머리글이 포함되어 있습니다.  요소 유형마다 Log Analytics 작업 영역에 있는 레코드에 대해 차별화된 시각화를 제공합니다.  요소의 항목을 클릭하면 상세 레코드를 제공하는 로그 검색을 수행합니다. |


## <a name="work-with-an-existing-view"></a>기존 보기로 작업
뷰 디자이너를 사용하여 만든 보기를 열 때 다음 표의 옵션을 포함하는 메뉴가 표시됩니다.

![개요 메뉴](media/log-analytics-view-designer/overview-menu.png)


| 옵션 | 설명 |
|:--|:--|
| 새로 고침   | 최신 데이터를 사용하여 보기를 새로 고칩니다. | 
| 분석 | [Advanced Analytics 포털](log-analytics-log-search-portals.md#advanced-analytics-portal)을 열고 로그 검색을 사용하여 데이터를 분석합니다(log-analytics-log-search-portals.md#advanced-analytics-portal). |
| Filter    | 보기에 포함된 데이터에 대한 시간 필터를 설정합니다. |
| 편집      | 뷰 디자이너에서 뷰를 열어 내용과 구성을 편집합니다.   |
| 복제     | 새 보기를 만든 후 뷰 디자이너에서 엽니다.  새 보기에는 원본과 동일한 이름 끝에 "복사본"이 추가된 이름이 적용됩니다. |


## <a name="create-a-new-view"></a>새 보기 만들기
Azure Portal에서 Log Analytics 작업 영역의 개요 페이지에 있는 뷰 디자이너 타일을 클릭하여 **뷰 디자이너**에서 새 보기를 만듭니다.

![뷰 디자이너 타일](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>뷰 디자이너 작업
새 보기를 만들거나 기존 보기를 편집할 경우 뷰 디자이너에서 작업하게 됩니다.  

뷰 디자이너에는 세 개의 창이 있습니다.  **디자인** 창에는 만들거나 편집하는 사용자 지정 보기가 포함되어 있습니다.  **제어** 창의 타일 및 요소를 **디자인** 창에 추가합니다.  **속성** 창에서는 타일 또는 선택한 요소의 속성을 표시합니다.

![뷰 디자이너](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>보기 타일 구성
사용자 지정 보기에는 타일 하나만 있을 수 있습니다.  **제어** 창에서 **타일** 탭을 선택하여 현재 타일을 확인하거나 다른 타일을 선택합니다.  **속성** 창에서는 현재 타일의 속성을 표시합니다.  [타일 참조](log-analytics-view-designer-tiles.md)의 세부 정보에 따라 타일 속성을 구성한 다음 **적용**을 클릭하여 변경 내용을 저장합니다.

### <a name="configure-visualization-parts"></a>시각화 요소 구성
보기에는 시각화 요소가 얼마든지 포함될 수 있습니다.  **보기** 탭, 보기에 추가할 시각화 요소를 차례로 선택합니다.  **속성** 창에서는 선택한 요소의 속성을 표시합니다.  [시각화 요소 참조](log-analytics-view-designer-parts.md)의 세부 정보에 따라 보기일 속성을 구성한 다음 **적용**을 클릭하여 변경 내용을 저장합니다.

보기에는 시각화 요소의 행 하나만 있습니다.  기존 요소를 클릭하여 새 위치로 끌어가면 보기에서 해당 요소를 다시 정렬할 수 있습니다.

요소 오른쪽 위 모서리의 **X** 단추를 클릭하면 보기에서 시각화 요소를 제거할 수 있습니다.


### <a name="menu-options"></a>메뉴 옵션
편집 모드에서 보기로 작업할 때 다음 표의 메뉴 옵션이 제공됩니다.

![편집 메뉴](media/log-analytics-view-designer/edit-menu.png)

| 옵션 | 설명 |
|:--|:--|
| 저장        | 변경 내용을 저장하고 보기를 닫습니다. |
| 취소      | 변경 내용을 취소하고 보기를 닫습니다. |
| 보기 삭제 | 보기를 삭제합니다. |
| 내보내기      | 보기를 다른 작업 영역으로 가져올 수 있는 [Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)으로 내보냅니다.  파일 이름은 보기 이름과 *omsview* 확장명으로 구성됩니다. |
| 가져오기      | 다른 작업 영역에서 내보낸 *omsview* 파일을 가져옵니다.  이렇게 하면 기존 보기의 구성을 덮어씁니다. |
| 복제       | 새 보기를 만든 후 뷰 디자이너에서 엽니다.  새 보기에는 원본과 동일한 이름 끝에 "복사본"이 추가된 이름이 적용됩니다. |
| 게시     | 보기를 [관리 솔루션](../operations-management-suite/operations-management-suite-solutions-resources-views.md)에 삽입할 수 있는 JSON 파일로 내보냅니다.  파일 이름은 보기 이름과 *json* 확장명으로 구성됩니다. 두 번째 파일이 JSON 파일에 정의된 리소스에 대한 값을 포함하는 확장명 *resjson*을 사용하여 만들어집니다 

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 [타일](log-analytics-view-designer-tiles.md) 추가
* 사용자 지정 보기에 [시각화 요소](log-analytics-view-designer-parts.md) 추가
