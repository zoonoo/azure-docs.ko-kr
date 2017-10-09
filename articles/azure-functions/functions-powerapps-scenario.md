---
title: "PowerApps에서 함수 호출 | Microsoft Docs"
description: "사용자 지정 커넥터를 만든 후 해당 커넥터를 사용하여 함수를 호출합니다."
services: functions
keywords: "클라우드 앱, 클라우드 서비스, PowerApps, 비즈니스 프로세스, 비즈니스 응용 프로그램"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 1e262fde37b68bcfcee3c974deb91bd07965de19
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="call-a-function-from-powerapps"></a>PowerApps에서 함수 호출
[PowerApps](https://powerapps.microsoft.com) 플랫폼은 비즈니스 전문가가 기존 응용 프로그램 코드 없이도 앱을 빌드할 수 있도록 디자인되었습니다. 전문 개발자는 Azure Functions를 사용하여 PowerApps 앱 작성자에게 기술적 세부 사항은 숨기면서 PowerApps의 기능을 확장할 수 있습니다.

이 항목의 앱은 풍차에 대한 유지 관리 시나리오를 토대로 빌드합니다. 이 항목에서는 [함수에 대한 OpenAPI 정의 만들기](functions-openapi-definition.md)에 정의된 함수를 호출하는 방법을 보여 줍니다. 이 함수는 풍차에 대한 응급 복구가 비용 효율적인지 여부를 확인합니다.

![PowerApps의 완성된 앱](media/functions-powerapps-scenario/finished-app.png)

Microsoft Flow에서 동일한 함수 호출에 대한 내용은 [Microsoft Flow에서 함수 호출](functions-flow-scenario.md)을 참조하세요.

이 항목에서는 다음 내용을 배웁니다.

> [!div class="checklist"]
> * Excel에서 샘플 데이터를 준비합니다.
> * API 정의를 내보냅니다.
> * API에 연결을 추가합니다.
> * 앱을 만들고 데이터 원본을 추가합니다.
> * 앱에서 데이터를 보기 위한 컨트롤을 추가합니다.
> * 함수를 호출하고 데이터를 표시하기 위한 컨트롤을 추가합니다.
> * 앱을 실행하여 복구가 비용 효율적인지 여부를 확인합니다.

## <a name="prerequisites"></a>필수 조건

+ Azure 계정과 동일한 로그인 자격 증명을 사용하는 활성 [PowerApps 계정](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md) 
+ Excel: Excel을 앱의 데이터 원본으로 사용합니다.
+ [함수에 대한 OpenAPI 정의 만들기](functions-openapi-definition.md) 자습서를 완료합니다.


## <a name="prepare-sample-data-in-excel"></a>Excel에서 샘플 데이터를 준비합니다.
먼저 앱에서 사용 하는 샘플 데이터를 준비합니다. 다음 표를 Excel로 복사합니다. 

| 제목      | 위도  | Longtitude  | LastServiceDate | MaxOutput | ServiceRequired | EstimatedEffort | InspectionNotes                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| Turbine 1  | 47.438401 | -121.383767 | 2/23/2017       | 2850      | 예             | 6               | 이번 달의 두 번째 문제입니다.       |
| Turbine 4  | 47.433385 | -121.383767 | 5/8/2017        | 5400      | 예             | 6               |                                            |
| Turbine 33 | 47.428229 | -121.404641 | 6/20/2017       | 2800      |                 |                 |                                            |
| Turbine 34 | 47.463637 | -121.358824 | 2/19/2017       | 2800      | 예             | 7               |                                            |
| Turbine 46 | 47.471993 | -121.298949 | 3/2/2017        | 1200      |                 |                 |                                            |
| Turbine 47 | 47.484059 | -121.311171 | 8/2/2016        | 3350      |                 |                 |                                            |
| Turbine 55 | 47.438403 | -121.383767 | 10/2/2016       | 2400      | 예             | 40               | 이 터빈을 위해 일부 부품을 들여왔습니다. |

1. Excel에서 해당 데이터를 선택하고 **홈** 탭에서 **표 서식**을 클릭합니다.

    ![표 서식](media/functions-powerapps-scenario/format-table.png)

1. 스타일을 선택하고 **확인**을 클릭합니다.

1. 표가 선택된 상태로 **디자인** 탭에서 **표 이름**으로 `Turbines`을 입력합니다.

    ![테이블 이름](media/functions-powerapps-scenario/table-name.png)

1. Excel 통합 문서를 저장합니다.

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>API에 연결 추가
사용자 지정 API(사용자 지정 커넥터라고도 함)는 PowerApps에서 사용할 수 있지만 앱에서 사용하려면 API에 연결되어 있어야 합니다.

1. [web.powerapps.com](https://web.powerapps.com)에서 **연결**을 클릭합니다.

    ![PowerApps 연결](media/functions-powerapps-scenario/powerapps-connections.png)

1. **새 연결**을 클릭하고 아래로 스크롤하여 **Turbine Repair** 커넥터로 이동한 후 클릭합니다.

    ![새 연결](media/functions-powerapps-scenario/new-connection.png)

1. API 키를 입력하고 **만들기**를 클릭합니다.

    ![연결 만들기](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> 다른 사용자와 앱을 공유하는 경우 앱에서 작업하거나 앱을 사용하는 각 사용자도 API에 연결하기 위해 API 키를 입력해야 합니다. 이 동작은 나중에 변경될 수 있으며 변경도면 이 항목도 업데이트될 것입니다.

## <a name="create-an-app-and-add-data-sources"></a>앱 만들기 및 데이터 원본 추가
이제 PowerApps에서 앱을 만들 준비가 되었으므로 Excel 데이터 및 사용자 지정 API를 앱의 데이터 원본으로 추가합니다.

1. [web.powerapps.com](https://web.powerapps.com)의 왼쪽 창에서 **새 앱**을 클릭합니다.

1. **빈 앱**에서 **휴대폰 레이아웃**을 클릭합니다.

    ![태블릿 앱 만들기](media/functions-powerapps-scenario/create-phone-app.png)

    앱은 웹용 PowerApps Studio에서 열립니다. 다음 그림에서는 PowerApps Studio의 다른 부분을 보여 줍니다. 이 이미지는 완성된 앱에 대한 것입니다. 가운데 창에는 맨 처음 표시되는 빈 화면이 나와 있습니다.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) 왼쪽 탐색 모음**: 각 화면의 모든 컨트롤에 대한 계층 보기가 표시됩니다.

    **(2) 가운데 창**: 작업 중인 화면이 표시됩니다.

    **(3) 오른쪽 창**: 레이아웃 및 데이터 원본과 같은 옵션을 설정합니다.

    **(4) 속성**: 수식이 적용되는 속성을 선택하는 드롭다운 목록

    **(5) 수식 입력줄**: 앱 동작을 정의하는 수식을 추가합니다(예: Excel).
    
    **(6) 리본**: 컨트롤을 추가하고 디자인 요소를 사용자 지정합니다.

1. Excel 파일을 데이터 원본으로 추가합니다.

    1. 오른쪽 창의 **데이터** 탭에서 **데이터 원본 추가**를 클릭합니다.

        ![데이터 원본 추가](media/functions-powerapps-scenario/add-data-source.png)

    1. **앱에 정적 데이터 추가**를 클릭합니다.

        ![데이터 원본 추가](media/functions-powerapps-scenario/add-static-data.png)

        일반적으로 외부 원본에서 데이터를 읽고 쓰지만, 이것은 샘플이므로 Excel 데이터를 정적 데이터로 추가합니다.

    1. 저장한 Excel 파일로 이동한 후 **Tubines** 표를 선택하고 **연결**을 클릭합니다.

        ![데이터 원본 추가](media/functions-powerapps-scenario/choose-table.png)

1. 사용자 지정 API를 데이터 원본으로 추가합니다.

    1. **데이터** 탭에서 **데이터 원본 추가**를 클릭합니다.

    1. **Turbine Repair**를 클릭합니다.

        ![Turbine repair 커넥터](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>앱에서 데이터를 보기 위한 컨트롤 추가
이제 데이터 원본을 앱에서 사용할 수 있으므로 터빈 데이터를 볼 수 있도록 앱에 화면을 추가합니다.

1. **홈** 탭에서 **새 화면** > **목록 화면**을 클릭합니다.

    ![목록 화면](media/functions-powerapps-scenario/list-screen.png)

    PowerApps는 항목을 표시하기 위한 *갤러리*와 검색, 정렬 및 필터링을 위한 기타 컨트롤이 포함된 화면을 추가합니다.

1. 제목 표시줄을 `Turbine Repair`로 변경하고 갤러리 크기를 조정하여 그 아래에 추가 컨트롤을 위한 공간을 확보합니다.

    ![제목 변경 및 갤러리 크기 조정](media/functions-powerapps-scenario/gallery-title.png)

1. 갤러리가 선택된 상태에서 오른쪽 창의 **데이터** 탭에서 데이터 원본을 **CustomGallerySample**에서 **Turbines**로 변경합니다.

    ![데이터 원본 변경](media/functions-powerapps-scenario/change-data-source.png)

    데이터 집합에는 이미지가 포함되어 있지 않으므로 다음에는 데이터에 더 잘 맞게 레이아웃을 변경합니다. 

1. 오른쪽 창에서 **레이아웃**을 **제목, 부제목 및 본문**으로 변경합니다.

    ![갤러리 레이아웃 변경](media/functions-powerapps-scenario/change-layout.png)

1. 오른쪽 창에서 수행하는 마지막 단계로, 갤러리에 표시되는 필드를 변경합니다.

    ![갤러리 필드 변경](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** = Title 

1. 갤러리가 선택된 상태에서 **TemplateFill** 속성을 수식 `If(ThisItem.IsSelected, Orange, White)`로 설정합니다.

    ![템플릿 채우기 수식](media/functions-powerapps-scenario/formula-fill.png)

    이제 선택된 갤러리 항목을 보다 쉽게 알 수 있습니다.

    ![선택한 항목](media/functions-powerapps-scenario/selected-item.png)

1. 앱에서 원래 화면은 필요하지 않습니다. 왼쪽 창에서 **Screen1**으로 마우스를 가져간 후 **. . .**를 클릭하고 **삭제**를 클릭합니다.

    ![삭제 화면](media/functions-powerapps-scenario/delete-screen.png)

일반적으로 프로덕션 앱에서 수행하는 기타 서식이 많이 있지만 이 시나리오에 중요한 함수 호출을 진행할 것입니다.

## <a name="add-controls-to-call-the-function-and-display-data"></a>함수를 호출하고 데이터를 표시하기 위한 컨트롤 추가
각 터빈에 대한 요약 데이터를 표시하는 앱이 만들어졌으므로 이제 만든 함수를 호출하고 반환되는 데이터를 표시하는 컨트롤을 추가해 보겠습니다. OpenAPI 정의에서 이름을 지정하는 방식에 따라 함수에 액세스합니다(이 경우 `TurbineRepair.CalculateCosts()`).

1. 리본의 **삽입** 탭에서 **단추**를 클릭합니다. 같은 탭에서 **레이블**을 클릭합니다.

    ![단추 및 레이블 삽입](media/functions-powerapps-scenario/insert-controls.png)

1. 단추 및 레이블을 갤러리 아래로 끌고 레이블 크기를 조정합니다. 

1. 단추 텍스트를 선택하고 `Calculate costs`로 변경합니다. 앱은 다음 이미지와 같아야 합니다.

    ![단추가 있는 앱](media/functions-powerapps-scenario/move-button-label.png)

1. 단추를 선택하고 단추의 **OnSelect** 속성에 대해 다음 수식을 입력합니다.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    이 수식은 단추를 클릭할 때 실행되고, 선택한 갤러리 항목의 **ServiceRequired** 값이 `Yes`인 경우 다음을 수행합니다.

    + *컬렉션* `DetermineRepair`를 지워 이전 호출의 데이터를 제거합니다. 컬렉션은 표 형식의 변수입니다.

    + 함수 `TurbineRepair.CalculateCosts()`를 호출하여 반환되는 데이터를 컬렉션에 할당합니다. 
    
        함수에 전달되는 값은 갤러리에서 선택된 항목에 대한 **EstimatedEffort** 및 **MaxOutput** 필드에서 가져옵니다. 이러한 필드는 갤러리에 표시되지 않지만 수식에서는 여전히 사용할 수 있습니다.

1. 레이블을 선택하고 레이블의 **Text** 속성에 대해 다음 수식을 입력합니다.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    이 수식은 `First()` 함수를 사용하여 `DetermineRepair` 컬렉션의 첫 번째(및 유일한) 행에 액세스합니다. 그런 후 함수가 반환하는 세 가지 값, 즉 `message`, `costToFix` 및 `revenueOpportunity`를 표시합니다. 이러한 값은 앱이 처음 실행되기 전에는 비어 있습니다.

    완성된 앱은 다음 이미지와 같아야 합니다.

    ![실행 전의 완성된 앱](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>앱 실행
앱이 완성되었습니다. 이제 앱을 실행하고 작동되는 함수 호출을 확인해 보겠습니다.

1. PowerApps Studio의 오른쪽 위 모서리에서 실행 단추를 클릭합니다. ![앱 실행 단추](media/functions-powerapps-scenario/f5-arrow-sm.png)에서도 확인할 수 있습니다.

1. **ServiceRequired** 값이 `Yes`인 터빈을 선택하고 **비용 계산** 단추를 클릭합니다. 다음 이미지와 유사한 결과가 표시됩니다.

    ![PowerApps의 완성된 앱](media/functions-powerapps-scenario/finished-app.png)

1. 다른 터빈에 대해서도 함수가 반환하는 결과를 확인해 봅니다.

## <a name="next-steps"></a>다음 단계
이 항목에서는 다음 내용을 배웁니다.

> [!div class="checklist"]
> * Excel에서 샘플 데이터를 준비합니다.
> * API 정의를 내보냅니다.
> * API에 연결을 추가합니다.
> * 앱을 만들고 데이터 원본을 추가합니다.
> * 앱에서 데이터를 보기 위한 컨트롤을 추가합니다.
> * 함수를 호출하고 데이터를 표시하기 위한 컨트롤 추가
> * 앱을 실행하여 복구가 비용 효율적인지 여부를 확인합니다.

PowerApps에 대한 자세한 내용은 [PowerApps 소개](https://powerapps.microsoft.com/tutorials/getting-started/)를 참조하세요.

Azure Functions를 사용하는 다른 흥미로운 시나리오에 대해 알아보려면 [Microsoft Flow에서 함수 호출](functions-flow-scenario.md) 및 [Azure Logic Apps와 통합하는 함수 만들기](functions-twitter-email.md)를 참조하세요.
