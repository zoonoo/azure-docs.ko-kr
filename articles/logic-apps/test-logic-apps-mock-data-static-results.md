---
title: 모의 데이터를 사용하여 논리 앱 테스트
description: 프로덕션 환경에 영향을 주지 않고 모의 데이터로 논리 앱을 테스트하기 위한 정적 결과 설정
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027706"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>정적 결과를 설정하여 모의 데이터로 논리 앱 테스트

논리 앱을 테스트할 때, 다양한 이유로 앱, 서비스, 시스템을 실제로 호출하거나 액세스할 준비가 되지 않았을 수 있습니다. 일반적으로 이러한 시나리오에서는 다른 조건 경로를 실행하거나, 오류를 강제 실행하거나, 특정 메시지 응답을 제공하거나, 일부 단계를 건너뛰어야 할 수 있습니다. 논리 앱에서 작업에 대한 정적 결과를 설정하면 해당 작업의 출력 데이터를 모의로 생성할 수 있습니다. 동작에서 정적 결과를 사용하도록 설정하면 작업이 실행되지 않고 모의 데이터가 대신 반환됩니다.

예를 들어 Outlook 365 메일 보내기 작업에 대해 정적 결과를 설정하는 경우, Logic Apps 엔진은 Outlook을 호출하고 이메일을 보내는 대신 정적 결과로 지정한 모의 데이터를 반환합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 정적 결과를 설정하려는 논리 앱

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>정적 결과 설정

1. 아직 없는 경우, [Azure Portal](https://portal.azure.com)의 Logic Apps Designer에서 논리 앱을 엽니다.

1. 정적 결과를 설정하려는 작업에서 다음 단계를 수행합니다. 

   1. 작업의 오른쪽 위 모서리에서 줄임표( *...* ) 단추를 선택하고 다음 예시와 같이 **정적 결과** 를 선택합니다.

      !["정적 결과" > "정적 결과 사용" 선택](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. **정적 결과 사용** 을 선택합니다. 필수(*) 속성의 경우, 작업의 응답에 대해 반환하려는 모의 출력 값을 지정합니다.

      예를 들어, HTTP 작업에 필요한 속성은 다음과 같습니다.

      | 속성 | 설명 |
      |----------|-------------|
      | **상태** | 반환할 작업 상태 |
      | **상태 코드** | 반환할 HTTP 상태 코드 |
      | **헤더** | 반환할 헤더 콘텐츠 |
      |||

      !["정적 결과 사용"을 선택합니다](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      모의 데이터를 JavaScript Object Notation(JSON) 형식으로 입력하려면 **JSON 모드로 전환** 을 선택합니다(!["JSON 모드로 전환" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) 선택).

   1. 선택적 속성의 경우, **선택적 필드 선택** 목록을 열고 모의하려는 속성을 선택합니다.

      ![선택적 속성 선택](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 준비가 되면 **완료** 를 선택합니다.

   작업의 오른쪽 위 모서리에 제목 표시줄에 정적 결과를 사용하도록 설정했음을 나타내는 테스트 비커 아이콘(![정적 결과 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))이 표시됩니다.

   ![활성화된 정적 결과를 보여주는 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   모의 데이터를 사용하는 이전 실행을 찾으려면, 이 항목의 뒷부분에 나오는 [정적 결과를 사용하는 실행 찾기](#find-runs-mock-data)를 참조하세요.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>이전 출력 다시 사용

논리 앱에 출력과 함께 이전 실행이 있는 경우, 모의 출력으로 재사용할 수 있습니다. 해당 실행의 출력을 복사하여 붙여 넣을 수 있습니다.

1. 아직 없는 경우, [Azure Portal](https://portal.azure.com)의 Logic Apps Designer에서 논리 앱을 엽니다.

1. 논리 앱의 주 메뉴에서 **개요** 를 선택합니다.

1. **실행 기록** 섹션에서 원하는 논리 앱 실행을 선택합니다.

1. 논리 앱의 워크플로에서 원하는 출력이 포함된 작업을 찾아 확장합니다.

1. **원시 출력 표시** 링크를 선택합니다.

1. 전체 JavaScript Object Notation(JSON) 개체 또는 사용하려는 특정 하위 섹션(예: 출력 섹션 또는 헤더 섹션)을 복사합니다.

1. [정적 결과 설정](#set-up-static-results)에서 작업에 대한 **정적 결과** 상자를 여는 단계를 수행합니다.

1. **정적 결과** 상자가 열리면 다음 단계 중 하나를 선택합니다.

   * 전체 JSON 개체를 붙여 넣으려면, **Json 모드로 전환**(![ "json 모드로 전환" 선택](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png))을 선택합니다.

     ![전체 개체에 대해 "JSON 모드로 전환"을 선택합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * JSON 섹션만 붙여 넣으려면, 해당 섹션의 레이블 옆에 있는 해당 섹션에 대해 **Json 모드로 전환** 을 선택합니다. 예를 들면 다음과 같습니다.

     ![출력에 대해 "JSON 모드로 전환"을 선택합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. JSON 편집기에서 이전에 복사한 JSON을 붙여 넣습니다.

   ![JSON 모드](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 모두 마쳤으면 **완료** 를 선택합니다. 또는 디자이너로 돌아가려면 **편집기 모드 전환**(!["편집기 모드 전환" 선택](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png))을 선택합니다.

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>정적 결과를 사용하는 실행 찾기

논리 앱의 실행 기록은 작업에서 정적 결과를 사용하는 실행을 식별합니다. 이러한 실행을 찾으려면 다음 단계를 수행합니다.

1. 논리 앱의 주 메뉴에서 **개요** 를 선택합니다. 

1. 오른쪽 창의 **실행 기록** 아래에서 **정적 결과** 열을 찾습니다. 

   결과가 포함된 작업을 포함하는 모든 실행에는 **정적 결과** 열이 **사용** 으로 설정됩니다.예를 들면 다음과 같습니다.

   ![실행 기록 - 정적 결과 열](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 정적 결과를 사용하는 작업을 보려면 **정적 결과** 열이 **사용** 으로 설정된 실행을 선택합니다.

   정적 결과를 사용하는 작업은 테스트 비커(![정적 결과 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) 아이콘을 표시합니다. 예를 들면 다음과 같습니다.

   ![실행 기록 - 정적 결과를 사용하는 작업](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>정적 결과 사용 안 함

정적 결과를 해제해도 마지막 설정에서 값이 사라지지는 않습니다. 따라서 다음에 정적 결과를 켜면 이전 값을 계속 사용할 수 있습니다.

1. 정적 출력을 사용하지 않도록 설정하려는 작업을 찾습니다. 작업의 오른쪽 위 모서리에서 테스트 비커 아이콘(![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))을 선택합니다.

   ![스크린샷은 테스트 비커 아이콘을 선택할 수 있는 HTTP 작업을 보여줍니다.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. **정적 결과 사용 안 함** > **완료** 를 선택합니다.

   ![스크린샷은 선택할 수 있는 정적 결과 사용 안 함 옵션을 보여줍니다.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>참조

기본 워크플로 정의에서 이 설정에 대한 자세한 내용은 [정적 결과 - 워크플로 정의 언어에 대한 스키마 참조](../logic-apps/logic-apps-workflow-definition-language.md#static-results) 및 [runtimeConfiguration.staticResult - 런타임 구성 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)을 참조하세요

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에 대해 자세히 알아봅니다