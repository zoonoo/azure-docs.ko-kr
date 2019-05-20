---
title: 모의 데이터-Azure Logic Apps를 사용 하 여 논리 앱 테스트
description: 프로덕션 환경에 영향을 주지 않고 모의 데이터를 사용 하 여 논리 앱을 테스트 하는 것에 대 한 정적 결과 설정
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597200"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>정적 결과 설정 하 여 모의 데이터를 사용 하 여 논리 앱 테스트

논리 앱을 테스트할 때 수 없습니다 실제로 호출 또는 다양 한 이유로 앱, 서비스 및 시스템에 액세스 하도록 준비 합니다. 일반적으로 이러한 시나리오에서는 해야 다른 조건을 경로 실행, 강제로 오류, 응답 본문을 특정 메시지를 제공 하거나도 몇 가지 단계를 건너뜁니다를 시도 합니다. 논리 앱에서 동작에 대 한 정적 결과 설정 하 여 해당 작업의 출력 데이터를 모의 수 있습니다. 작업에 대 한 정적 결과 사용 하도록 설정 하면 작업을 실행 하지 않습니다 하지만 대신 모의 데이터를 반환 합니다.

예를 들어, 설정한 경우 Outlook 365에 대 한 정적 결과 보내기 메일 작업, Logic Apps 엔진은 정적 결과로 지정 된 모의 데이터 보다는 Outlook을 호출 하 고 전자 메일 보내기에 반환 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 정적 결과 설정 하려는 논리 앱

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>정적 결과 설정

1. 아직 하지 않은 경우에 [Azure portal](https://portal.azure.com), Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 정적 결과 설정 하려는 동작에서 다음이 단계를 수행 합니다. 

   1. 작업의 오른쪽 위 모서리에서 줄임표를 선택 (*...* ) 단추를 선택한 **정적 결과**예를 들어:

      !["정적 결과" 선택 > "정적 결과 사용 하도록 설정"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 선택할 **정적 결과 사용 하도록 설정**합니다. 필수 (*) 속성에 대해 반환할 작업의 응답에 대 한 원하는 모의 출력 값을 지정 합니다.

      예를 들어, 다음은 HTTP 동작에 대 한 필수 속성입니다.

      | 자산 | 설명 |
      |----------|-------------|
      | **상태** | 반환할 작업의 상태 |
      | **상태 코드** | 반환할 특정 상태 코드 |
      | **헤더** | 반환할 헤더 콘텐츠 |
      |||

      !["정적 결과 사용 하도록 설정" 선택](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      모의 데이터를 개체 JSON (JavaScript Notation) 형식으로 입력 하려면 선택 **JSON 모드를 전환할** (![선택 "JSON 모드로 전환"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. 선택적 속성을 엽니다는 **선택적 필드 선택** 목록 및 모의로 진행 하려면 속성을 선택 합니다.

      ![선택적 속성을 선택 합니다.](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 저장할 준비가 때 선택할 **수행**합니다.

   작업의 오른쪽 위 모서리에 있는 제목 표시줄에 이제 테스트 비 커 아이콘을 보여 줍니다 (![정적 결과 대 한 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), 활성화 한 정적 결과 나타냅니다.

   ![아이콘 표시 정적 결과 사용 하도록 설정](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   모의 데이터를 사용 하는 이전 실행 참조 하세요 [정적 결과 사용 하는 실행을 찾을](#find-runs-mock-data) 이 항목의에서 뒷부분에 있습니다.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>이전 출력 다시 사용

논리 앱을 이전에 모의 출력으로 다시 사용할 수 있습니다 하는 출력을 사용 하 여 실행 하면 복사 하에서 실행 되는 출력을 붙여넣습니다.

1. 아직 하지 않은 경우에 [Azure portal](https://portal.azure.com), Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 주 메뉴에서 선택 **개요**합니다.

1. 에 **실행 기록** 섹션에서 원하는 논리 앱이 하를 실행 합니다.

1. 논리 앱 워크플로에서 찾아 저장할 출력이 있는 동작을 확장 합니다.

1. 선택 된 **원시 출력 표시** 링크 합니다.

1. 전체 개체 JSON (JavaScript Notation) 개체 또는 예를 들어 사용 하려는 특정 하위 섹션, 출력 섹션 또는 만으로도 헤더 섹션을 복사 합니다.

1. 열기에 대 한 단계는 **정적 결과** 상자에서 동작에 대 한 [정적 결과 설정](#set-up-static-results)합니다.

1. 후 합니다 **정적 결과** 상자가 열리고, 두 단계를 선택 합니다.

   * 완전 한 JSON 개체를 붙여 넣으려면 선택 **JSON 모드를 전환할** (![선택 "JSON 모드로 전환"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![완전 한 개체에 대 한 "JSON 모드로 전환" 선택](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 선택 섹션의 레이블 옆에 있는 JSON 섹션 방금 붙여 **JSON 모드를 전환할** 예를 들어가 섹션에 대 한 합니다.

     ![출력에 대 한 "JSON 모드로 전환" 선택](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. JSON 편집기에서 이전에 복사한 JSON를 붙여 넣습니다.

   ![JSON 모드](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 모두 마쳤으면 **완료**를 선택합니다. 또는 디자이너에 반환할 선택할 **스위치 편집기 모드** (![선택 "편집기 모드로 전환"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>정적 결과 사용 하는 실행 찾기

논리 앱의 실행 기록 실행 작업을 정적 결과 사용 하는 위치를 식별 합니다. 이러한 실행을 찾으려면 다음이 단계를 수행 합니다.

1. 논리 앱의 주 메뉴에서 선택 **개요**합니다. 

1. 오른쪽 창에서 아래 **실행 기록**를 찾을 합니다 **정적 결과** 열입니다. 

   결과 사용 하 여 작업을 포함 하는 모든 실행에는 **정적 결과** 열으로 **Enabled**예를 들어:

   ![실행 기록-정적 결과 열](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 정적 결과 사용 하는 작업을 보려면 위치를 원하는 실행을 선택 합니다 **정적 결과** 열으로 설정 됩니다 **Enabled**합니다.

   정적 결과 사용 하는 작업 테스트 비 커 표시 (![정적 결과 대 한 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) 아이콘을 예를 들어:

   ![기록-정적 결과 사용 하는 작업 실행](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>정적 결과 사용 하지 않도록 설정

정적 결과 해제 하지 버리는 값에서 마지막으로 설정 합니다. 따라서 다음에 정적 결과에 설정 하면 이전 값을 사용 하 여 계속 수 있습니다.

1. 정적 출력을 사용 하지 않도록 설정 하려는 작업을 찾습니다. 작업의 오른쪽 위 모서리에 있는 테스트 비 커 아이콘을 선택 합니다 (![정적 결과 대 한 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![정적 결과 사용 하지 않도록 설정](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 선택할 **정적 결과 사용 하지 않도록 설정** > **수행**합니다.

   ![정적 결과 사용 하지 않도록 설정](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>참조

기본 워크플로 정의에서이 설정에 대 한 자세한 내용은 참조 하세요. [정적 결과-워크플로 정의 언어에 대 한 스키마 참조](../logic-apps/logic-apps-workflow-definition-language.md#static-results) 고 [runtimeConfiguration.staticResult-런타임 구성 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)