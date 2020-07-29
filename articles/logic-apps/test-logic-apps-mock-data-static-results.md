---
title: 모의 데이터를 사용하여 논리 앱 테스트
description: 프로덕션 환경에 영향을 주지 않고 모의 데이터로 논리 앱을 테스트 하기 위한 정적 결과 설정
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74790270"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>정적 결과를 설정 하 여 모의 데이터로 논리 앱 테스트

논리 앱을 테스트할 때 다양 한 이유로 실제로 앱, 서비스 및 시스템을 호출 하거나 액세스할 준비가 되지 않았을 수 있습니다. 일반적으로 이러한 시나리오에서는 다른 조건 경로를 실행 하거나, 오류를 발생 시키고, 특정 메시지 응답 본문을 제공 하거나, 몇 가지 단계를 건너뛰도록 시도 해야 할 수 있습니다. 논리 앱의 동작에 대 한 정적 결과를 설정 하면 해당 작업에서 모의 출력 데이터를 사용할 수 있습니다. 동작에서 정적 결과를 사용 하도록 설정 하면 작업이 실행 되지 않지만 모의 데이터를 대신 반환 합니다.

예를 들어 Outlook 365 메일 보내기 작업에 대 한 정적 결과를 설정 하면 Logic Apps 엔진은 Outlook을 호출 하 고 전자 메일을 보내는 대신 정적 결과로 지정한 모의 데이터만 반환 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 정적 결과를 설정 하려는 논리 앱

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>정적 결과 설정

1. 아직 [Azure Portal](https://portal.azure.com)하지 않은 경우 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 정적 결과를 설정 하려는 작업에서 다음 단계를 수행 합니다. 

   1. 작업의 오른쪽 위 모서리에서 줄임표 (*...*) 단추를 선택 하 고 **정적 결과**를 선택 합니다. 예를 들면 다음과 같습니다.

      !["정적 결과" > "정적 결과 사용"을 선택 합니다.](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. **정적 결과 사용**을 선택 합니다. 필수 (*) 속성의 경우 작업의 응답에 대해 반환 하려는 모의 출력 값을 지정 합니다.

      예를 들어 HTTP 작업에 대 한 필수 속성은 다음과 같습니다.

      | 속성 | 설명 |
      |----------|-------------|
      | **Status** | 반환할 작업의 상태입니다. |
      | **상태 코드** | 반환할 특정 상태 코드 |
      | **헤더** | 반환할 헤더 콘텐츠입니다. |
      |||

      !["정적 결과 사용"을 선택 합니다.](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      JavaScript Object Notation (JSON) 형식으로 모의 데이터를 입력 하려면 **Json 모드로 전환** ( ![ "json 모드로 전환" 선택)을 선택 ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) 합니다.

   1. 선택적 속성의 경우 **선택적 필드 선택** 목록을 열고 mock에 사용할 속성을 선택 합니다.

      ![선택적 속성 선택](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 저장할 준비가 되 면 **완료**를 선택 합니다.

   작업의 오른쪽 위 모서리에서 제목 표시줄에는 테스트 반쯤 아이콘 ( ![ 정적 결과 아이콘 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) )이 표시 되며,이는 정적 결과를 사용 하도록 설정 되었음을 나타냅니다.

   ![활성화 된 정적 결과를 보여 주는 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   모의 데이터를 사용 하는 이전 실행을 찾으려면이 항목의 뒷부분에 나오는 [정적 결과를 사용 하는 실행 찾기](#find-runs-mock-data) 를 참조 하세요.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>이전 출력 다시 사용

논리 앱에 출력과 함께 이전 실행이 있는 경우 모의 출력으로 재사용할 수 있습니다. 그러면 해당 실행의 출력을 복사 하 여 붙여 넣을 수 있습니다.

1. 아직 [Azure Portal](https://portal.azure.com)하지 않은 경우 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 주 메뉴에서 **개요**를 선택 합니다.

1. **실행 기록** 섹션에서 원하는 논리 앱 실행을 선택 합니다.

1. 논리 앱의 워크플로에서 원하는 출력이 포함 된 작업을 찾아 확장 합니다.

1. **원시 출력 표시** 링크를 선택 합니다.

1. 전체 JavaScript Object Notation (JSON) 개체 또는 사용 하려는 특정 하위 섹션 (예: 출력 섹션 또는 헤더 섹션)을 복사 합니다.

1. [정적 결과 설정](#set-up-static-results)에서 작업에 대 한 **정적 결과** 상자를 여는 단계를 수행 합니다.

1. **정적 결과** 상자가 열리면 다음 단계 중 하나를 선택 합니다.

   * 전체 JSON 개체를 붙여넣으려면 **Json 모드로 전환** ( ![ "json 모드로 전환" 선택)을 선택 합니다 ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) .

     ![전체 개체에 대해 "JSON 모드로 전환"을 선택 합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * JSON 섹션만 붙여넣으려면 해당 섹션의 레이블 옆에 있는 해당 섹션에 대해 **Json 모드로 전환** 을 선택 합니다. 예를 들면 다음과 같습니다.

     ![출력에 대해 "JSON 모드로 전환"을 선택 합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. JSON 편집기에서 이전에 복사한 JSON을 붙여넣습니다.

   ![JSON 모드](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 모두 마쳤으면 **완료**를 선택합니다. 또는 디자이너로 돌아가려면 **편집기 모드 전환** ( ![ "편집기 모드 전환" 선택)을 선택 ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) 합니다.

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>정적 결과를 사용 하는 실행 찾기

논리 앱의 실행 기록은 작업에서 정적 결과를 사용 하는 실행을 식별 합니다. 이러한 실행을 찾으려면 다음 단계를 수행 합니다.

1. 논리 앱의 주 메뉴에서 **개요**를 선택 합니다. 

1. 오른쪽 창의 **실행 기록**에서 **정적 결과** 열을 찾습니다. 

   결과를 포함 하는 모든 실행은 **정적 결과** 열을 **Enabled**로 설정 합니다. 예를 들면 다음과 같습니다.

   ![실행 기록-정적 결과 열](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 정적 결과를 사용 하는 작업을 보려면 **정적 결과** 열이 **사용**으로 설정 된 위치에서 실행을 선택 합니다.

   정적 결과를 사용 하는 작업에는 테스트 반쯤 ( ![ 정적 결과 아이콘 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ) 아이콘이 표시 됩니다. 예를 들면 다음과 같습니다.

   ![실행 기록-정적 결과를 사용 하는 작업](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>정적 결과 사용 안 함

정적 결과를 끄면 마지막 설정의 값이 발생 하지 않습니다. 따라서 다음에 정적 결과를 켜면 이전 값을 계속 사용할 수 있습니다.

1. 정적 출력을 사용 하지 않도록 설정할 작업을 찾습니다. 작업의 오른쪽 위 모서리에서 테스트 반쯤 아이콘 ( ![ 정적 결과 아이콘)을 선택 ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) 합니다.

   ![정적 결과 사용 안 함](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. **정적 결과 사용 안 함**  >  **Done**을 선택 합니다.

   ![정적 결과 사용 안 함](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>참고

기본 워크플로 정의의이 설정에 대 한 자세한 내용은 [정적 결과-워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md#static-results) 및 [runtimeConfiguration 구성 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) 에 대 한 스키마 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 에 대 한 자세한 정보