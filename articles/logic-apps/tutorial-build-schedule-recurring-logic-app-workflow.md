---
title: Azure에서 일정 기반 자동화 워크플로 빌드
description: 자습서 - Azure Logic Apps를 사용하여 클라우드 서비스에서 통합되는 일정 기반의 되풀이 자동화 워크플로를 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: aad271875abb9024a1ecc7f45018c04d8c79ce95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842566"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>자습서: Azure Logic Apps를 사용하여 일정 기반의 되풀이 자동화 워크플로 만들기

이 자습서에서는 되풀이 일정에 따라 실행되는 워크플로를 자동화하는 예제 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하는 방법을 보여줍니다. 특히 이 예제 논리 앱은 두 위치 간의 트래픽을 포함하여 이동 시간을 확인하며, 평일 아침마다 실행됩니다. 시간이 특정 제한을 초과하면 논리 앱에서는 이동 시간 및 목적지에 도달하는 데 걸린 추가 시간이 포함된 이메일을 보냅니다. 워크플로에는 일정 기반 트리거에 이어 Bing Maps 작업, 데이터 연산 작업, 제어 흐름 작업 및 이메일 알림 작업으로 이어지는 다양한 단계가 포함되어 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 빈 논리 앱을 만듭니다.
> * 논리 앱의 일정을 지정하는 되풀이 트리거를 추가합니다.
> * 경로의 이동 시간을 가져오는 Bing Maps 작업을 추가합니다.
> * 변수를 만들고, 이동 시간을 초에서 분 단위로 변환하고, 해당 결과를 변수에 저장하는 작업을 추가합니다.
> * 이동 시간을 지정된 제한과 비교하는 조건을 추가합니다.
> * 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다.

여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

![예제 논리 앱 워크플로의 간략한 개요를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Logic Apps에서 지원하는 이메일 공급자(예: Office 365 Outlook, Outlook.com 또는 Gmail)의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](/connectors/). 이 빠른 시작에서는 회사 또는 학교 계정으로 Office 365 Outlook을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다를 수 있습니다.

  > [!IMPORTANT]
  > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

* 경로의 이동 시간 정보를 가져오려면 Bing 지도 API에 대한 액세스 키가 필요합니다. 이 키를 가져오려면 [Bing 지도 키를 가져오는 방법](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)의 단계를 수행합니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 홈 페이지에서 **리소스 만들기**를 선택합니다.

1. Azure Marketplace 메뉴에서 **통합** > **논리 앱**을 선택합니다.

   !["통합" 및 "논리 앱"이 선택된 Azure Marketplace 메뉴를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. **논리 앱** 창에서, 만들려는 논리 앱에 대해 설명하는 정보를 여기에 입력합니다.

   ![논리 앱 만들기 창 및 새 논리 앱에 대해 입력해야 하는 정보를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **구독** | <*Azure-subscription-name*> | Azure 구독 이름. 이 예에서는 `Pay-As-You-Go`를 사용합니다. |
   | **리소스 그룹** | LA-TravelTime-RG | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름입니다. 이 예제는 새 리소스 그룹 `LA-TravelTime-RG`를 만듭니다. |
   | **이름** | LA-TravelTime | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`(`, `)`) 및 마침표(`.`)만 포함할 수 있는 논리 앱의 이름입니다. 이 예에서는 `LA-TravelTime`를 사용합니다. |
   | **위치** | 미국 서부 | 논리 앱 정보를 저장할 지역입니다. 이 예에서는 `West US`를 사용합니다. |
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. |
   ||||

1. 완료되면 **검토 + 만들기**를 선택합니다. Azure가 논리 앱에 대한 정보의 유효성을 검사한 후에는 **만들기**를 선택합니다.

1. Azure가 앱을 배포한 후 **리소스로 이동**을 선택합니다.

   Azure가 Logic Apps 템플릿 선택 창을 엽니다. 이 창은 소개 비디오, 일반적으로 사용되는 트리거 및 논리 앱 템플릿 패턴을 보여줍니다.

1. 비디오와 일반 트리거를 지나 **템플릿** 섹션이 나올 때까지 아래로 스크롤하고, **비어 있는 논리 앱**을 선택합니다.

   !["비어 있는 논리 앱"이 선택된 Logic Apps 템플릿 선택 창을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

다음으로, 지정된 일정에 따라 워크플로를 실행하는 되풀이 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 모든 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="add-the-recurrence-trigger"></a>되풀이 트리거 추가

1. Logic Apps 디자이너 검색 상자에 `recurrence`를 입력하고 **Recurrence**라는 트리거를 선택합니다.

   ![Logic Apps 디자이너 검색 상자에 검색어 "recurrence"가 입력되었고 "트리거" 목록에서 "Recurrence" 트리거가 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **되풀이** 셰이프에서 **줄임표**( **...** ) 단추, **이름 바꾸기**를 차례로 선택합니다. `Check travel time every weekday morning` 설명이 포함되도록 트리거 이름을 바꿉니다.

   ![줄임표 단추가 선택되었고, "설정" 목록이 열려 있고, "이름 바꾸기" 명령이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 트리거 내에서 이러한 속성을 여기에 설명된 대로 변경합니다.

   ![트리거의 간격 및 빈도 변경을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **간격** | 예 | 1 | 검사 간에 대기하는 간격의 수 |
   | **빈도** | 예 | Week | 되풀이에 사용할 시간 단위 |
   |||||

1. **간격** 및 **빈도** 아래에서 **새 매개 변수 추가** 목록을 열고, 다음 속성을 선택하여 트리거에 추가합니다.

   * **요일 선택**
   * **시간 선택**
   * **분 선택**

   !["새 매개 변수 추가" 목록이 열려 있고 "요일 선택", "시간 선택" 및 "분 선택" 속성이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 이제 여기서 설명한 대로 추가 속성의 값을 설정합니다.

   ![다음 표에 설명된 값으로 설정된 추가 속성을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **요일 선택** | 월요일,화요일,수요일,목요일,금요일 | 이 설정은 **빈도**를 **주**로 설정한 경우에만 사용할 수 있습니다. |
   | **시간 선택** | 7,8,9 | 이 설정은 **빈도**를 **주** 또는 **일**로 설정한 경우에만 사용할 수 있습니다. 이 되풀이의 실행 시간을 선택합니다. 이 예제에서는 `7`, `8` 및 `9`시 표시에 실행됩니다. |
   | **분 선택** | 0,15,30,45 | 이 설정은 **빈도**를 **주** 또는 **일**로 설정한 경우에만 사용할 수 있습니다. 이 되풀이를 실행할 분을 선택합니다. 이 예제에서는 0시 표시에서 시작하여 15분마다 실행됩니다. |
   ||||

   이 트리거는 평일 오전 7시부터 오전 9시 45분까지 15분마다 실행됩니다. **미리 보기** 상자는 되풀이 일정을 표시합니다. 자세한 내용은 [작업 및 워크플로 예약](../connectors/connectors-native-recurrence.md) 및 [워크플로 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)를 참조하세요.

1. 지금은 트리거의 세부 정보를 숨기기 위해 셰이프의 제목 표시줄 내부를 클릭하여 셰이프를 축소합니다.

   ![축소된 트리거 셰이프를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 논리 앱이 Azure Portal에서 작동 중이지만, 지정된 일정에 따라 트리거하는 것 외에는 아무 것도 하지 않습니다. 따라서 트리거가 실행되면 응답하는 작업을 추가해 보겠습니다.

## <a name="get-the-travel-time-for-a-route"></a>경로의 이동 시간 가져오기

이제 트리거가 생겼으니, 두 장소 사이의 이동 시간을 가져오는 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다. Logic Apps는 이 정보를 쉽게 얻을 수 있도록 Bing 지도 API에 대한 커넥터를 제공합니다. 이 작업을 시작하기 전에, 이 자습서의 필수 구성 섹션에서 설명한 Bing 지도 API 키를 갖고 있는지 확인합니다.

1. 논리 앱 디자이너의 되풀이 트리거에서 **새 단계**를 선택합니다.

1. **작업 선택**에서 **표준**을 선택합니다. 검색 상자에 `bing maps`를 입력하고 **경로 가져오기**라는 작업을 선택합니다.

   !["bing maps" 작업으로 필터링된 "작업 선택" 목록과 "경로 가져오기" 작업이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing 지도가 연결되지 않으면 연결을 만들라는 메시지가 나타납니다. 연결 정보를 보이는 대로 입력하고 **만들기**를 선택합니다.

   ![지정된 연결 이름과 Bing Maps API 키를 사용하는 Bing Maps 연결 상자를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **연결 이름** | 예 | BingMapsConnection | 연결 이름을 입력합니다. 이 예에서는 `BingMapsConnection`를 사용합니다. |
   | **API 키** | 예 | <*Bing-Maps-API-key*> | 이전에 받은 Bing Maps API 키를 입력합니다. Bing 지도 키가 없는 경우 [키를 가져오는 방법](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)을 알아보세요. |
   |||||

1. `Get route and travel time with traffic` 설명이 포함되도록 작업 이름을 바꿉니다.

1. 작업 내에서 **새 매개 변수 목록 추가**를 열고, 다음 속성을 선택합니다.

   * **Optimize**
   * **Distance unit**
   * **Travel mode**

   !["Optimize", "Distance unit" 및 "Travel mode" 속성이 선택된 "경로 가져오기..." 작업을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 여기에 설명된 대로 속성 값을 입력합니다.

   !["경로 가져오기" 작업에 대한 추가 속성 값을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **Waypoint 1** | 예 | <*시작 위치*> | 경로의 출발지입니다. 이 예제에서는 예제 출발지 주소를 지정합니다. |
   | **Waypoint 2** | 예 | <*마지막 위치*> | 경로의 도착지입니다. 이 예제에서는 예제 도착지 주소를 지정합니다. |
   | **Optimize** | 예 | timeWithTraffic | 거리, 현재 트래픽 하에서의 이동 시간 등 경로를 최적화하기 위한 매개 변수. 매개 변수 값 **timeWithTraffic**을 선택합니다. |
   | **Distance unit** | 예 | <*원하는 단위*> | 경로의 거리 단위. 이 예제에서는 단위로 **마일**을 사용합니다. |
   | **Travel mode** | 예 | Driving | 경로의 이동 모드. **운전** 모드를 선택합니다. |
   |||||

   이러한 매개 변수와 값에 대한 자세한 내용은 [경로 계산](/bingmaps/rest-services/routes/calculate-a-route)을 참조하세요.

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

다음으로, 현재 이동 시간을 초가 아닌 분으로 변환하여 저장할 수 있도록 변수를 만듭니다. 이렇게 하면 변환을 반복하지 않을 수 있으며 이후 단계에서 값을 좀 더 수월하게 사용할 수 있습니다. 

## <a name="create-a-variable-to-store-travel-time"></a>이동 시간을 저장하는 변수 만들기

경우에 따라 워크플로에서 데이터에 대한 작업을 실행한 다음, 이후 작업에서 결과를 사용할 수 있습니다. 이러한 결과를 간편하게 재사용 또는 참조할 수 있도록 결과를 저장하려면 데이터 처리 후 결과를 저장하는 변수를 만들면 됩니다. 변수는 논리 앱의 최상위 수준에서만 만들 수 있습니다.

기본적으로 **경로 가져오기** 작업은 **이동 시간 트래픽** 속성에서 트래픽이 포함된 현재 이동 시간을 초 단위로 반환합니다. 이 값을 분 단위로 변환하여 저장하면 나중에 변환 작업 없이 간편하게 값을 재사용할 수 있습니다.

1. 디자이너의 **경로 가져오기** 작업에서 **새 단계**를 선택합니다.

1. **작업 선택**에서 **기본 제공**을 선택합니다. 검색 상자에 `variables`를 입력하고 **변수 초기화**라는 작업을 선택합니다.

   !["변수 초기화" 작업이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. `Create variable to store travel time` 설명이 포함되도록 이 작업의 이름을 바꿉니다.

1. 이 표와 표 아래의 단계에 표시된 대로 변수 정보를 입력합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | travelTime | 변수의 이름입니다. 이 예에서는 `travelTime`를 사용합니다. |
   | **형식** | 예 | 정수 | 변수의 데이터 형식 |
   | **값** | 예 | 현재 이동 시간을 초 단위에서 분 단위로 변환하는 식입니다(이 표 아래의 단계 참조). | 변수의 초기 값 |
   |||||

   1. **값** 속성에 대한 식을 만들려면 동적 콘텐츠 목록이 표시되도록 상자 내부를 클릭합니다. 필요한 경우 동적 목록이 나타날 때까지 브라우저를 확장합니다. 동적 콘텐츠 목록에서 **식**을 선택합니다. 그러면 식 편집기가 표시됩니다.

      ![동적 콘텐츠 목록을 여는 "값" 속성 내부에 커서가 있는 "변수 초기화" 작업을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      동적 콘텐츠 목록은 워크플로의 후속 작업에 대한 입력으로 선택할 수 있는 이전 작업의 출력을 보여줍니다. 동적 콘텐츠 목록에는 식에서 연산을 수행하는 함수를 선택하는 데 사용할 수 있는 식 편집기가 포함되어 있습니다. 이 식 편집기는 동적 콘텐츠 목록에만 제공됩니다.

   1. 식 편집기에서 `div(,60)` 식을 입력합니다.

      !["div(,60)" 식이 입력된 식 편집기를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 식 내부에서 커서를 왼쪽 괄호( **(** )와 쉼표( **,** ) 사이에 놓고 **동적 콘텐츠**를 선택합니다.

      !["div(,60)" 식에서 커서를 어디에 놓고 "동적 콘텐츠"를 선택해야 하는지 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 동적 콘텐츠 목록에서 **이동 시간 트래픽** 속성 값을 선택합니다.

      !["이동 시간 트래픽" 속성 값을 선택한 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 식 내에서 속성 값이 확인되면 **확인**을 선택합니다.

      !["확인" 단추를 선택한 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      이제 **값** 속성은 다음과 같이 표시됩니다.

      ![식이 확인된 "값" 속성을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 논리 앱을 저장합니다.

다음으로, 현재 이동 시간이 특정 제한보다 큰지 확인하는 조건을 추가합니다.

## <a name="compare-the-travel-time-with-limit"></a>이동 시간을 제한과 비교

1. **이동 시간을 저장하는 변수 만들기** 작업에서 **새 단계**를 선택합니다.

1. **작업 선택**에서 **기본 제공**을 선택합니다. 검색 상자에 `condition`를 입력합니다. 그리고 작업 목록에서 **조건**이라는 작업을 선택합니다.

   !["조건" 작업이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. `If travel time exceeds limit` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 여기서 설명한 대로 **travelTime** 속성 값이 지정된 제한을 초과하는지 확인하는 조건을 작성합니다.

   1. 조건의 왼쪽에서 **값 선택** 상자 내부를 클릭합니다.

   1. 표시되는 동적 콘텐츠 목록의 **변수**에서 **travelTime** 속성을 선택합니다.

      ![조건의 왼쪽에 표시되는 "값 선택" 상자에서 동적 콘텐츠 목록이 열려 있고 "travelTime" 속성이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 가운데 비교 상자에서 **보다 큼** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에서 `15` 제한을 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![이동 시간을 지정된 제한과 비교하는 완료된 조건을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 논리 앱을 저장합니다.

다음으로, 이동 시간이 제한을 초과하면 실행할 작업을 추가합니다.

## <a name="send-email-when-limit-exceeded"></a>제한을 초과하면 이메일 발송

이제 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다. 이 이메일에는 현재 이동 시간 및 지정된 경로로 이동하는 데 필요한 추가 시간이 포함됩니다.

1. 조건의 **True** 분기에서 **작업 추가**를 선택합니다.

1. **작업 선택**에서 **표준**을 선택합니다. 검색 상자에 `send email`를 입력합니다. 목록에서 많은 결과를 반환하므로, 목록을 필터링할 수 있도록 먼저 원하는 이메일 커넥터를 선택합니다.

   예를 들어 Outlook 이메일 계정을 사용하는 경우 계정 유형에 맞는 커넥터를 선택합니다.

   * Azure 회사 또는 학교 계정인 경우 **Office 365 Outlook**을 선택합니다.
   * Microsoft 개인 계정인 경우 **Outlook.com**을 선택합니다.

   이 예제에서는 Office 365 Outlook을 선택하여 진행합니다.

   !["표준" 범주 및 "Office 365 Outlook" 커넥터가 선택된 "작업 선택 목록"을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. 커넥터의 작업이 표시되면 이메일을 보내는 작업을 선택합니다. 예를 들어 다음과 같습니다.

   !["이메일 보내기" 작업이 선택된 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 아직 연결이 설정되지 않은 경우 로그인하고 메시지가 표시되면 이메일 계정에 대한 액세스를 인증합니다.

   Azure Logic Apps가 이메일 계정에 연결합니다.

1. `Send email with travel time` 설명이 포함되도록 작업 이름을 바꿉니다.

1. **받는 사람** 속성에 받는 사람의 이메일 주소를 입력합니다. 메일 주소를 사용하여 테스트할 수 있습니다.

1. **제목** 속성에서 이메일 제목을 지정하고, 다음 단계를 수행하여 **travelTime** 변수를 포함합니다.

   1. `Current travel time (minutes):` 텍스트를 입력하고 후행 공백을 하나 둡니다. 동적 콘텐츠 목록이 열린 상태로 유지되도록 커서를 계속해서 **제목** 상자에 둡니다.

   1. 동적 콘텐츠 목록의 **변수** 제목에서 **자세히 보기**를 선택하면 **travelTime**이라는 변수가 표시됩니다.

      !["변수" 섹션 및 "자세히 보기"가 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > **제목** 속성에는 문자열 값이 필요한데 **travelTime**은 정수 값이므로 동적 콘텐츠 목록에 **travelTime** 변수가 자동으로 표시되지 않습니다.

      !["travelTime" 변수가 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **본문** 속성의 경우 다음 단계에 따라 이메일 본문의 콘텐츠를 지정합니다.

   1. `Add extra travel time (minutes):` 텍스트를 입력하고 후행 공백을 하나 둡니다. 동적 콘텐츠 목록이 열린 상태로 유지되도록 커서를 계속해서 **본문** 상자에 둡니다.

   1. 동적 콘텐츠 목록에서 **식**을 선택합니다. 그러면 식 편집기가 표시됩니다.

      !["식"이 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 제한을 초과하는 시간(분)을 계산할 수 있도록 식 편집기에서 `sub(,15)`를 입력합니다. 

      !["sub(,15)" 식이 입력된 식 편집기를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 식 내부에서 커서를 왼쪽 괄호( **(** )와 쉼표( **,** ) 사이에 놓고 **동적 콘텐츠**를 선택합니다.

      !["sub(,15)" 식에서 커서를 어디에 놓고 "동적 콘텐츠"를 선택해야 하는지 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **변수** 아래에서 **travelTime**을 선택합니다.

      !["travelTime" 변수가 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 식 내에서 속성이 확인되면 **확인**을 선택합니다.

      !["확인"이 선택된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      이제 **본문** 속성은 다음과 같이 표시됩니다.

      ![이메일 작업의 "본문" 속성에서 식이 확인된 동적 콘텐츠 목록을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 논리 앱을 저장합니다.

다음으로, 이 예제와 비슷한 논리 앱을 테스트하고 실행합니다.

![완료된 예제 논리 앱 워크플로를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

논리 앱을 수동으로 시작하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

* 현재 이동 시간이 제한 내에서 유지되면 논리 앱은 다른 작업을 수행하지 않고, 다시 확인하기 전에 다음 간격을 기다립니다. 

* 현재 이동 시간이 제한을 초과하면 현재 이동 시간 및 제한을 초과한 시간(분)이 포함된 이메일을 받게 됩니다. 논리 앱에서 다음과 같은 이메일을 보냅니다.

  ![현재 이동 시간 및 지정된 제한을 초과하는 추가 이동 시간을 보고하는 예제 이메일을 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > 이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. 첫 번째 스케줄러 기반 되풀이 논리 앱을 만들고 실행하셨습니다. 

**되풀이** 트리거를 사용하는 다른 논리 앱을 만들려면 논리 앱을 만든 후에 사용할 수 있는 다음 템플릿을 확인하세요.

* 매일 미리 알림 받기.
* 오래된 Azure Blob 삭제.
* Azure Storage 큐에 메시지 추가.

## <a name="clean-up-resources"></a>리소스 정리

앱을 해제하거나 삭제할 때까지 논리 앱이 계속 실행됩니다. 논리 앱 샘플이 더 이상 필요하지 않으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제합니다.

1. Azure Portal의 검색 상자에 앞에서 만든 리소스 그룹의 이름을 입력합니다. 결과의 **리소스 그룹**에서 리소스 그룹을 선택합니다.

   이 예제에서는 `LA-TravelTime-RG`라는 리소스 그룹을 만들었습니다.

   !["la-travel-time-rg"를 입력하고 **LA-TravelTime-RG**를 선택한 Azure 검색 상자를 보여주는 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Azure 홈 페이지의 **최근 리소스**에 리소스 그룹이 표시되는 경우 홈 페이지에서 그룹을 선택할 수 있습니다.

1. 리소스 그룹 메뉴에서 **개요**가 선택되었는지 확인합니다. **개요** 창의 도구 모음에서 **리소스 그룹 삭제**를 선택합니다.

   ![리소스 그룹의 "개요" 창과 이 창의 도구 모음에서 "리소스 그룹 삭제"를 선택한 스크린샷](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 표시되는 확인 창에서 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 지정된 일정(평일 오전)에 따라 트래픽을 검사하고, 이동 시간이 지정된 제한을 초과하면 작업(이메일 보내기)을 수행하는 논리 앱을 만들었습니다. 지금부터 Azure 서비스, Microsoft 서비스 및 기타 SaaS(Software-as-a-Service) 앱을 통합하여 메일 그룹 요청을 보내 승인을 요청하는 논리 앱 빌드 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [메일 그룹 요청 관리](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
