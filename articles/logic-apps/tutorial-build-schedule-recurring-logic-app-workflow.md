---
title: 자동화된 일정 기반 워크플로 빌드
description: 자습서 - Azure Logic Apps를 사용하여 자동화된 일정 기반 되풀이 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 41f7b1309a9c7fa9a5f2abb3e2e59f08ef31382d
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124853"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>자습서: Azure Logic Apps를 사용하여 자동화된 일정 기반 되풀이 워크플로 만들기

이 자습서에서는 [논리 앱](../logic-apps/logic-apps-overview.md)을 빌드하고 일정에 따라 실행되는 되풀이 워크플로를 자동화하는 방법을 보여 줍니다. 특히 이 논리 앱 예제는 평일 아침마다 실행되며, 두 위치 간의 트래픽을 포함하여 이동 시간을 확인합니다. 시간이 특정 제한을 초과하면 논리 앱에서는 이동 시간 및 목적지에 도달하는 데 걸리는 추가 시간이 포함된 이메일을 보냅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 빈 논리 앱을 만듭니다.
> * 논리 앱의 일정을 지정하는 되풀이 트리거를 추가합니다.
> * 경로의 이동 시간을 가져오는 Bing Maps 작업을 추가합니다.
> * 변수를 만들고, 이동 시간을 초에서 분 단위로 변환하고, 해당 결과를 변수에 저장하는 작업을 추가합니다.
> * 이동 시간을 지정된 제한과 비교하는 조건을 추가합니다.
> * 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다.

여기까지 모두 마치면 논리 앱이 이 워크플로와 비슷하게 보입니다.

![상위 수준 논리 앱 워크플로 개요](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정에 가입합니다](https://azure.microsoft.com/free/).

* Logic Apps에서 지원하는 이메일 공급자(예: Office 365 Outlook, Outlook.com 또는 Gmail)의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](/connectors/). 이 빠른 시작에서는 Office 365 Outlook 계정을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다를 수 있습니다.

  > [!IMPORTANT]
  > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

* 경로의 이동 시간 정보를 가져오려면 Bing 지도 API에 대한 액세스 키가 필요합니다. 이 키를 가져오려면 [Bing 지도 키를 가져오는 방법](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)의 단계를 수행합니다.

## <a name="create-your-logic-app"></a>논리 앱 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱**을 차례로 선택합니다.

   ![논리 앱 리소스 만들기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. 그림에 설명된 대로 **논리 앱 만들기** 아래에서 논리 앱에 대한 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

   ![논리 앱에 대한 정보 제공](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | 속성 | Value | 설명 |
   |----------|-------|-------------|
   | **이름** | LA-TravelTime | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`(`, `)`) 및 마침표(`.`)만 포함할 수 있는 논리 앱의 이름입니다. 이 예에서는 "LA-TravelTime"을 사용합니다. |
   | **구독** | <*your-Azure-subscription-name*> | Azure 구독 이름 |
   | **리소스 그룹** | LA-TravelTime-RG | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름입니다. 이 예에서는 "LA-TravelTime-RG"를 사용합니다. |
   | **위치** | 미국 서부 | 논리 앱 정보를 저장할 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. |
   ||||

1. Azure가 앱을 배포한 후 Azure 도구 모음에서 배포된 논리 앱에 대해 **알림** > **리소스로 이동**을 선택합니다.

   ![새 논리 앱 리소스로 이동](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   또는 검색 상자에 이름을 입력하여 논리 앱을 찾아 선택할 수 있습니다.

   Logic Apps 디자이너가 열려 소개 비디오 및 일반적으로 사용되는 트리거와 논리 앱 패턴이 포함된 페이지가 표시됩니다. **템플릿** 아래에서 **빈 논리 앱**을 선택합니다.

   ![빈 논리 앱 템플릿 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

다음으로, 지정된 일정에 따라 실행되는 되풀이 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 추가합니다. 모든 논리 앱은 특정 이벤트가 발생하거나 새 데이터가 특정 조건을 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 자세한 내용은 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="add-the-recurrence-trigger"></a>되풀이 트리거 추가

1. 논리 앱 디자이너의 검색 상자에서 필터로 "되풀이"를 입력합니다. **트리거** 목록에서 **되풀이** 트리거를 선택합니다.

   !["되풀이" 트리거 추가](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **되풀이** 셰이프에서 **줄임표**( **...** ) 단추, **이름 바꾸기**를 차례로 선택합니다. `Check travel time every weekday morning` 설명이 포함되도록 트리거 이름을 바꿉니다.

   ![되풀이 트리거 설명 이름 바꾸기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 트리거 내에서 다음 속성을 변경합니다.

   ![되풀이 트리거의 간격 및 빈도 변경](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **간격** | 예 | 1 | 검사 간에 대기하는 간격의 수 |
   | **빈도** | 예 | Week | 되풀이에 사용할 시간 단위 |
   |||||

1. **간격** 및 **빈도** 아래에서 **새 매개 변수 추가** 목록을 열고, 다음 속성을 선택하여 트리거에 추가합니다.

   * **요일 선택**
   * **시간 선택**
   * **분 선택**

   ![되풀이 트리거에 대한 속성 추가](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 이제 여기서 설명한 대로 추가 속성의 값을 설정합니다.

   ![일정 및 되풀이 세부 정보 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | 속성 | Value | 설명 |
   |----------|-------|-------------|
   | **요일 선택** | 월요일,화요일,수요일,목요일,금요일 | **빈도**가 "주"로 설정된 경우에만 사용 가능 |
   | **시간 선택** | 7,8,9 | **빈도**가 "주" 또는 "일"로 설정된 경우에만 사용할 수 있습니다. 이 되풀이를 실행할 시간을 선택합니다. 이 예에서는 7, 8 및 9시 표시에 실행됩니다. |
   | **분 선택** | 0,15,30,45 | **빈도**가 "주" 또는 "일"로 설정된 경우에만 사용할 수 있습니다. 이 되풀이를 실행할 분을 선택합니다. 이 예에서는 0시 표시에서 시작하여 15분마다 실행됩니다. |
   ||||

   이 트리거는 평일 오전 7시부터 오전 9시 45분까지 15분마다 실행됩니다. **미리 보기** 상자는 되풀이 일정을 표시합니다. 자세한 내용은 [작업 및 워크플로 예약](../connectors/connectors-native-recurrence.md) 및 [워크플로 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)를 참조하세요.

1. 지금은 트리거의 세부 정보를 숨기려면 셰이프의 제목 표시줄 내부를 클릭합니다.

   ![세부 정보를 숨기려면 셰이프 축소](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 논리 앱이 라이브 상태지만 되풀이 외에는 아무 것도 수행하지 않습니다. 따라서 트리거가 실행되면 응답하는 작업을 추가해 보겠습니다.

## <a name="get-the-travel-time-for-a-route"></a>경로의 이동 시간 가져오기

이제 트리거가 생겼으니, 두 장소 사이의 이동 시간을 가져오는 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다. Logic Apps는 이 정보를 쉽게 얻을 수 있도록 Bing 지도 API에 대한 커넥터를 제공합니다. 이 작업을 시작하기 전에, 이 자습서의 필수 구성 섹션에서 설명한 Bing 지도 API 키를 갖고 있는지 확인합니다.

1. 논리 앱 디자이너의 트리거 아래에서 **새 단계**를 선택합니다.

1. **작업 선택** 아래에서 **표준**을 선택합니다. 검색 상자에서 필터로 "bing maps"를 입력하고, **경로 가져오기** 작업을 선택합니다.

   !["경로 가져오기" 작업 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing 지도가 연결되지 않으면 연결을 만들라는 메시지가 나타납니다. 다음 연결 세부 정보를 제공하고, **만들기**를 선택합니다.

   ![Bing Maps API에 대한 연결 만들기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **연결 이름** | 예 | BingMapsConnection | 연결 이름을 입력합니다. 이 예에서는 "BingMapsConnection"을 사용합니다. |
   | **API 키** | 예 | <*your-Bing-Maps-key*> | 이전에 받은 Bing 지도 키를 입력합니다. Bing 지도 키가 없는 경우 [키를 가져오는 방법](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)을 알아보세요. |
   |||||

1. `Get route and travel time with traffic` 설명이 포함되도록 작업 이름을 바꿉니다.

1. 작업 내에서 **새 매개 변수 목록 추가**를 열고, 다음 속성을 선택하여 작업에 추가합니다.

   * **Optimize**
   * **Distance unit**
   * **Travel mode**

   !["경로 가져오기" 작업에 속성 추가](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 이제 여기서 설명한 대로 작업의 속성 값을 설정합니다.

   !["경로 가져오기" 작업에 대한 세부 정보 제공](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **Waypoint 1** | 예 | <*시작 위치*> | 경로의 출발지 |
   | **Waypoint 2** | 예 | <*마지막 위치*> | 경로의 도착지 |
   | **Optimize** | 예 | timeWithTraffic | 거리, 현재 트래픽 하에서의 이동 시간 등 경로를 최적화하기 위한 매개 변수. "timeWithTraffic" 매개 변수를 선택합니다. |
   | **Distance unit** | 예 | <*원하는 단위*> | 경로의 거리 단위. 이 예에서는 단위로 "마일"을 사용합니다. |
   | **Travel mode** | 예 | Driving | 경로의 이동 모드. "구동" 모드를 선택합니다. |
   ||||

   이러한 매개 변수에 대한 자세한 내용은 [경로 계산](/bingmaps/rest-services/routes/calculate-a-route)을 참조하세요.

1. 논리 앱을 저장합니다.

다음으로, 현재 이동 시간을 초가 아닌 분으로 변환하여 저장할 수 있도록 변수를 만듭니다. 이렇게 하면 변환을 반복하지 않을 수 있으며 이후 단계에서 값을 좀 더 수월하게 사용할 수 있습니다. 

## <a name="create-a-variable-to-store-travel-time"></a>이동 시간을 저장하는 변수 만들기

경우에 따라 워크플로에서 데이터에 대한 작업을 실행한 다음, 이후 작업에서 결과를 사용할 수 있습니다. 이러한 결과를 간편하게 재사용 또는 참조할 수 있도록 결과를 저장하려면 데이터 처리 후 결과를 저장하는 변수를 만들면 됩니다. 변수는 논리 앱의 최상위 수준에서만 만들 수 있습니다.

기본적으로 이전의 **경로 가져오기** 작업에서는 **이동 시간 트래픽** 속성에서 트래픽이 포함된 현재 이동 시간을 초 단위로 반환합니다. 이 값을 분 단위로 변환하여 저장하면 나중에 변환 작업 없이 간편하게 값을 재사용할 수 있습니다.

1. **경로 가져오기** 작업 아래에서 **새 단계**를 선택합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 "변수"를 입력하고, **변수 초기화** 작업을 선택합니다.

   !["변수 초기화" 작업 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. `Create variable to store travel time` 설명이 포함되도록 이 작업의 이름을 바꿉니다.

1. 여기에 설명된 대로 변수에 대한 세부 정보를 입력합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **이름** | 예 | travelTime | 변수의 이름입니다. 이 예에서는 "travelTime"을 사용합니다. |
   | **형식** | 예 | 정수 | 변수의 데이터 형식 |
   | **값** | 예| 현재 이동 시간을 초 단위에서 분 단위로 변환하는 식입니다(이 표 아래의 단계 참조). | 변수의 초기 값 |
   ||||

   1. **값** 속성에 대한 식을 만들려면 동적 콘텐츠 목록이 표시되도록 상자 내부를 클릭합니다. 필요한 경우 목록이 나타날 때까지 브라우저를 확장합니다. 동적 콘텐츠 목록에서 **식**을 선택합니다.

      !["변수 초기화" 작업에 대한 정보 제공](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      일부 편집 상자 내부를 클릭하면 동적 콘텐츠 목록이 표시됩니다. 이 목록에는 워크플로에서 입력으로 사용할 수 있는 이전 작업의 속성이 표시됩니다. 동적 콘텐츠 목록에는 작업을 실행할 함수를 선택할 수 있는 식 편집기가 있습니다. 이 식 편집기는 동적 콘텐츠 목록에만 나타납니다.

   1. 식 편집기에서 `div(,60)` 식을 입력합니다.

      !["div(,60)" 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 왼쪽 괄호( **(** )와 쉼표( **,** ) 사이의 식 내부에 커서를 놓습니다. 
   **동적 콘텐츠**를 선택합니다.

      ![커서를 이동하여 "동적 콘텐츠"를 선택합니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 동적 콘텐츠 목록에서 **이동 시간 트래픽**을 선택합니다.

      !["이동 시간 트래픽" 속성 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 식 내에서 속성 값이 확인되면 **확인**을 선택합니다.

      ![식 작성을 완료하려면 "확인"을 선택합니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      이제 **값** 속성은 다음과 같이 표시됩니다.

      !["값" 속성이 확인된 식과 함께 나타납니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 논리 앱을 저장합니다.

다음으로, 현재 이동 시간이 특정 제한보다 큰지 확인하는 조건을 추가합니다.

## <a name="compare-the-travel-time-with-limit"></a>이동 시간을 제한과 비교

1. 이전 작업 아래에서 **새 단계**를 선택합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 “조건”을 입력합니다. 작업 목록에서 **조건** 작업을 선택합니다.

   !["조건" 작업 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. `If travel time exceeds limit` 설명이 포함되도록 조건 이름을 바꿉니다.

1. 여기서 설명한 대로 **travelTime** 속성 값이 지정된 제한을 초과하는지 확인하는 조건을 작성합니다.

   1. 조건에서 조건의 왼쪽에 있는 **값 선택** 상자 내부를 클릭합니다.

   1. 표시되는 동적 콘텐츠 목록의 **변수** 아래에서 **travelTime** 속성을 선택합니다.

      ![조건의 왼쪽 빌드](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 가운데 비교 상자에서 **보다 큼** 연산자를 선택합니다.

   1. 조건의 오른쪽에 있는 **값 선택** 상자에서 `15` 제한을 입력합니다.

      완료되면 조건이 다음 예와 같습니다.

      ![이동 시간을 확인하는 완성된 조건](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 논리 앱을 저장합니다.

다음으로, 이동 시간이 제한을 초과하면 실행할 작업을 추가합니다.

## <a name="send-email-when-limit-exceeded"></a>제한을 초과하면 이메일 발송

이제 이동 시간이 제한을 초과하면 이메일을 보내는 작업을 추가합니다. 이 이메일에는 현재 이동 시간 및 지정된 경로로 이동하는 데 필요한 추가 시간이 포함됩니다.

1. 조건의 **true인 경우** 분기에서 **작업 추가**를 선택합니다.

1. **작업 선택** 아래에서 **표준**을 선택합니다. 검색 상자에서 "이메일 보내기"를 입력합니다. 목록에서 많은 결과를 반환하므로 먼저 원하는 이메일 커넥터를 선택합니다. 예를 들어 다음과 같습니다.

   ![원하는 이메일 커넥터를 선택합니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Azure 회사 또는 학교 계정인 경우 **Office 365 Outlook**을 선택합니다.
   * Microsoft 개인 계정인 경우 **Outlook.com**을 선택합니다.

1. 커넥터의 작업이 표시되면 사용하려는 "이메일 보내기 작업"을 선택합니다. 예를 들어 다음과 같습니다.

   !["전자 메일 보내기" 작업 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 연결이 없는 경우 이메일 계정에 로그인하라는 메시지가 표시됩니다.

   Logic Apps는 전자 메일 계정에 대한 연결을 만듭니다.

1. `Send email with travel time` 설명이 포함되도록 작업 이름을 바꿉니다.

1. **받는 사람** 상자에 받는 사람의 이메일 주소를 입력합니다. 테스트를 위해 자신의 이메일 주소를 사용합니다.

1. **제목** 상자에서 이메일 제목을 지정하고, **travelTime** 변수를 포함합니다.

   1. `Current travel time (minutes):` 텍스트를 입력하고 후행 공백을 하나 둡니다. 

   1. 동적 콘텐츠 목록의 **변수** 아래에서 **자세히 보기**를 선택합니다.

      !["travelTime" 변수 찾기](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. **변수** 아래에 **travelTime**이 표시되면 **travelTime**을 선택합니다.

      ![제목 텍스트 및 이동 시간을 반환하는 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **본문** 상자에서 이메일 본문의 내용을 지정합니다.

   1. `Add extra travel time (minutes):` 텍스트를 입력하고 후행 공백을 하나 둡니다.

   1. 동적 콘텐츠 목록에서 **식**을 선택합니다.

      ![이메일 본문에 대한 식 작성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 식 편집기에서 제한을 초과하는 시간(분)을 계산할 수 있도록 ```sub(,15)``` 식을 입력합니다.

      ![추가 이동 시간(분)을 계산하는 식 입력](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 왼쪽 괄호( **(** )와 쉼표( **,** ) 사이의 식 내부에 커서를 놓습니다. **동적 콘텐츠**를 선택합니다.

      ![추가 이동 시간(분)을 계산하는 식을 계속 작성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **변수** 아래에서 **travelTime**을 선택합니다.

      ![식에 사용할 "travelTime" 속성 선택](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 식 내에서 속성이 확인되면 **확인**을 선택합니다.

      !["본문" 속성이 확인되면 "확인"을 선택합니다.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      이제 **본문** 속성은 다음과 같이 표시됩니다.

      ![식에서 확인된 "본문" 속성](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 논리 앱을 저장합니다.

다음으로, 이 예제와 비슷하게 표시되는 논리 앱을 테스트합니다.

![완료된 예제 논리 앱 워크플로](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>논리 앱 실행

수동으로 논리 앱을 시작하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

* 현재 이동 시간이 제한 내에서 유지되면 논리 앱은 다른 작업을 수행하지 않고, 다시 확인하기 전에 다음 간격을 기다립니다. 

* 현재 이동 시간이 제한을 초과하면 현재 이동 시간 및 제한을 초과한 시간(분)이 포함된 이메일을 받게 됩니다. 논리 앱에서 다음과 같은 이메일을 보냅니다.

![이동 시간을 표시하는 이메일 발송 예제](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

이메일을 받지 못한 경우 이메일의 정크 폴더를 확인합니다. 이메일 정크 필터가 이러한 종류의 메일을 리디렉션할 수 있습니다. 그렇지 않으면 논리 앱이 올바르게 실행되는지 모르는 경우 [논리 앱 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

축하드립니다. 첫 번째 스케줄러 기반 되풀이 논리 앱을 만들고 실행하셨습니다. 

**되풀이** 트리거를 사용하는 다른 논리 앱을 만들려면 논리 앱을 만든 후에 사용할 수 있는 다음 템플릿을 확인하세요.

* 매일 미리 알림 받기.
* 오래된 Azure Blob 삭제.
* Azure Storage 큐에 메시지 추가.

## <a name="clean-up-resources"></a>리소스 정리

논리 앱 샘플이 더 이상 필요하지 않으면 논리 앱 및 관련 리소스가 포함된 리소스 그룹을 삭제합니다. 

1. 주 Azure 메뉴에서 **리소스 그룹**으로 이동하여 논리 앱의 리소스 그룹을 선택합니다.

1. 리소스 그룹 메뉴에서 **개요** > **리소스 그룹 삭제**를 차례로 선택합니다. 

   !["개요" > "리소스 그룹 삭제"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 리소스 그룹 이름을 입력하여 확인하고, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 지정된 일정(평일 오전)에 따라 트래픽을 검사하고, 이동 시간이 지정된 제한을 초과하면 작업(이메일 보내기)을 수행하는 논리 앱을 만들었습니다. 지금부터는 Azure 서비스, Microsoft 서비스 및 기타 SaaS 앱을 통합하여 메일 그룹 요청을 보내 승인을 요청하는 논리 앱 빌드 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [메일 그룹 요청 관리](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
