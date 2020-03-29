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
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790270"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>정적 결과를 설정하여 모의 데이터로 논리 앱 테스트

논리 앱을 테스트할 때 여러 가지 이유로 실제로 앱, 서비스 및 시스템에 전화를 걸거나 액세스할 준비가 되지 않았을 수 있습니다. 일반적으로 이러한 시나리오에서는 다른 조건 경로를 실행하거나, 오류를 강제로 하거나, 특정 메시지 응답 본문제공을 제공하거나, 일부 단계를 건너뛰어야 할 수 있습니다. 논리 앱에서 작업에 대한 정적 결과를 설정하면 해당 작업의 출력 데이터를 모의할 수 있습니다. 작업에 정적 결과를 사용하도록 설정하면 작업이 실행되지 않지만 대신 모의 데이터를 반환합니다.

예를 들어 Outlook 365 send 메일 작업에 대 한 정적 결과를 설정 하는 경우 논리 애플 리 케이 션 엔진은 Outlook을 호출 하 고 전자 메일을 보내는 대신 정적 결과로 지정 한 모의 데이터를 반환 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* [논리 앱을 만드는 방법에](../logic-apps/quickstart-create-first-logic-app-workflow.md) 대한 기본 지식

* 정적 결과를 설정하려는 논리 앱

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>정적 결과 설정

1. 아직 하지 않은 경우 [Azure Portal에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 정적 결과를 설정하려는 작업에서 다음 단계를 따르십시오. 

   1. 작업의 오른쪽 위 모서리에서 타원 *(...*) 버튼을 선택하고 **정적 결과를 선택합니다.**

      !["정적 결과" > "정적 결과 사용" 선택](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. **정적 결과 활성화를 선택합니다.** 필요한(*) 속성의 경우 작업의 응답에 대해 반환할 모의 출력 값을 지정합니다.

      예를 들어 HTTP 작업에 필요한 속성은 다음과 같습니다.

      | 속성 | 설명 |
      |----------|-------------|
      | **상태** | 반환할 작업의 상태 |
      | **상태 코드** | 반환할 특정 상태 코드 |
      | **헤더** | 반환할 헤더 콘텐츠 |
      |||

      !["정적 결과 사용" 선택](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      자바스크립트 객체 표기술(JSON) 형식으로 모의 데이터를 입력하려면 **JSON 모드로** 전환("JSON![모드로 전환"선택)을](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)선택합니다.

   1. 선택적 속성의 경우 **선택적 필드 선택** 목록을 열고 모의할 속성을 선택합니다.

      ![선택적 속성 선택](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 저장할 준비가 되면 **완료를**선택합니다.

   작업의 오른쪽 위 모서리에 제목 표시줄에 정적 결과를 사용하도록 설정했음을 나타내는 테스트 비커 아이콘(정적![결과에](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)대한 아이콘)이 표시됩니다.

   ![활성화된 정적 결과를 표시하는 아이콘](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   모의 데이터를 사용하는 이전 실행을 찾으려면 이 항목의 후반부에서 [정적 결과를 사용하는 실행 찾기를](#find-runs-mock-data) 참조하십시오.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>이전 출력 재사용

논리 앱에 모의 출력으로 재사용할 수 있는 출력이 있는 이전 실행이 있는 경우 해당 실행의 출력을 복사하여 붙여넣기할 수 있습니다.

1. 아직 하지 않은 경우 [Azure Portal에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 기본 메뉴에서 **개요를**선택합니다.

1. 실행 **기록** 섹션에서 원하는 논리 앱 실행을 선택합니다.

1. 논리 앱의 워크플로에서 원하는 출력이 있는 작업을 찾아 확장합니다.

1. 원시 출력 표시 링크를 **선택합니다.**

1. 전체 자바스크립트 객체 표기법(JSON) 개체 또는 사용하려는 특정 하위 섹션(예: 출력 섹션 또는 헤더 섹션)을 복사합니다.

1. [정적 결과 설정에서](#set-up-static-results)작업에 대한 **정적 결과** 상자를 여는 단계를 따릅니다.

1. 정적 **결과** 상자가 열리면 다음 단계를 선택합니다.

   * 전체 JSON 객체를 붙여 넣려면 **JSON 모드로 전환** ("JSON![모드로 전환"선택)을](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)선택합니다.

     ![전체 개체에 대해 "JSON 모드로 전환"을 선택합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 예를 들어 해당 섹션의 레이블 옆에 JSON 섹션만 붙여넣은 경우 해당 섹션의 **JSON 모드로** 전환(예:

     ![출력에 대해 "JSON 모드로 전환"을 선택합니다.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. JSON 편집기에서 이전에 복사한 JSON을 붙여넣습니다.

   ![JSON 모드](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 모두 마쳤으면 **완료**를 선택합니다. 또는 디자이너로 돌아가려면 **스위치 편집기** ![모드("편집기](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)전환 모드 선택)를 선택합니다.

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>정적 결과를 사용하는 실행 찾기

논리 앱의 실행 기록은 동작이 정적 결과를 사용하는 실행을 식별합니다. 이러한 실행을 찾으려면 다음 단계를 따르십시오.

1. 논리 앱의 기본 메뉴에서 **개요를**선택합니다. 

1. 오른쪽 창에서 실행 **기록**아래에서 **정적 결과** 열을 찾습니다. 

   결과와 함께 작업을 포함 하는 모든 실행에는 **정적 결과** 열 **사용**으로 설정 되어 있습니다.

   ![실행 기록 - 정적 결과 열](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 정적 결과를 사용하는 작업을 보려면 **정적 결과** 열이 **사용 설정된**영역으로 설정된 실행을 선택합니다.

   정적 결과를 사용하는 작업에는 테스트 비커(정적![](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)결과에 대한 아이콘) 아이콘이 표시됩니다.

   ![기록 실행 - 정적 결과를 사용하는 작업](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>정적 결과 사용 안 함

정적 결과를 끄면 마지막 설정에서 값이 사라지지 않습니다. 따라서 다음에 정적 결과를 켜면 이전 값을 계속 사용할 수 있습니다.

1. 정적 출력을 사용하지 않도록 설정할 작업을 찾습니다. 작업의 오른쪽 위 모서리에서 테스트 비커 아이콘(정적![결과에](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)대한 아이콘)을 선택합니다.

   ![정적 결과 사용 안 함](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. **정적 결과** > 사용 안**함 완료를 선택합니다.**

   ![정적 결과 사용 안 함](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>참고

기본 워크플로 정의에서 이 설정에 대한 자세한 내용은 정적 결과 - 워크플로 정의 언어 및 [런타임구성.staticResult - 런타임 구성 설정에](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) [대한 스키마 참조를](../logic-apps/logic-apps-workflow-definition-language.md#static-results) 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱에](../logic-apps/logic-apps-overview.md) 대해 자세히 알아보기