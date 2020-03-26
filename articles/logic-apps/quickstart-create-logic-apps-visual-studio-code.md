---
title: Visual Studio Code를 사용하여 작업 자동화
description: VS Code(Visual Studio Code)를 사용하여 논리 앱 기본 JSON 정의를 만들거나 편집합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 819a60887743f39d3c2ffab3c955b2980cee2725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74784836"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 논리 앱 워크플로 정의 만들기 및 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 Visual Studio Code를 사용하여 조직 및 기업 전체에서 앱, 데이터, 시스템 및 서비스를 통합하기 위한 작업, 워크플로 및 프로세스를 자동화하는 데 도움이 되는 논리 앱을 만들고 관리할 수 있습니다. 이 빠른 시작에서는 코드 기반 환경을 통해 논리 앱에 JSON(JavaScript Object Notation)을 사용하는 기본 워크플로 정의를 만들고 편집하는 방법을 보여줍니다. Azure에 이미 배포된 기존 논리 앱에서 작업할 수도 있습니다.

[Azure Portal](https://portal.azure.com) 및 Visual Studio에서 이러한 동일 작업을 수행할 수도 있지만, 논리 앱 정의를 이미 숙지하고 있으며 코드에서 직접 작업하려는 경우 Visual Studio Code를 사용하면 작업을 더 빨리 시작할 수 있습니다. 예를 들어 이미 생성된 논리 앱을 사용하거나 사용하지 않도록 설정하고 삭제하고 새로 고칠 수 있습니다. 또한 Linux, Windows 및 Mac과 같은 Visual Studio Code가 실행되는 모든 개발 플랫폼의 논리 앱 및 통합 계정에서 작업할 수 있습니다.

이 문서의 경우 기본적인 개념에 대해 더 중점적으로 설명하는 [빠른 시작](../logic-apps/quickstart-create-first-logic-app-workflow.md)에서 동일한 논리 앱을 만들 수 있습니다. Visual Studio Code에서 논리 앱은 다음 예제와 같이 표시됩니다.

![논리 앱 워크플로 정의 예제](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

시작하기 전에 이러한 항목이 있는지 확인합니다.

* Azure 계정 및 구독이 없는 경우 [체험 Azure 계정에 가입](https://azure.microsoft.com/free/)합니다.

* [논리 앱 워크플로 정의](../logic-apps/logic-apps-workflow-definition-language.md) 및 JSON에 설명된 해당 구조에 대한 기본 지식

  Logic Apps를 처음 사용하는 경우 Azure Portal에서 첫 번째 논리 앱을 만들고 기본적인 개념에 대해 더 중점적으로 설명하는 [빠른 시작](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 확인해 보세요.

* Azure 및 Azure 구독에 로그인하기 위해 웹에 액세스.

* 다음 도구가 없으면 다운로드하여 설치합니다.

  * 체험인 [Visual Studio Code 버전 1.25.1 이상](https://code.visualstudio.com/).

  * Azure Logic Apps용 Visual Studio Code 확장

    이 확장은 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps)에서 다운로드하여 설치하거나 Visual Studio Code 내에서 직접 설치할 수 있습니다. 설치한 후 Visual Studio Code를 다시 로드해야 합니다.

    ![“Azure Logic Apps용 Visual Studio Code 확장” 찾기](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    확장이 올바르게 설치되었는지 확인하기 위해 Visual Studio Code 도구 모음에 표시되는 Azure 아이콘을 선택합니다.

    ![확장이 올바르게 설치되어 있는지 확인](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    자세한 내용은 [확장 마켓플레이스](https://code.visualstudio.com/docs/editor/extension-gallery)를 참조하세요. 이 확장의 오픈 소스 버전에 참여하려면 [GitHub의 Visual Studio Code용 Azure Logic Apps 확장](https://github.com/Microsoft/vscode-azurelogicapps)을 방문합니다.

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Azure에 로그인

1. Visual Studio Code를 엽니다. Visual Studio Code 도구 모음에서 Azure 아이콘을 선택합니다.

   ![Visual Studio Code 도구 모음에서 Azure 아이콘을 선택합니다.](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Azure 창의 **Logic Apps** 아래에서 **Azure에 로그인**을 선택합니다. Microsoft 로그인 페이지에서 로그인하라는 메시지가 표시되면 Azure 계정으로 로그인합니다.

   ![“Azure에 로그인” 선택](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. 로그인 시간이 평소보다 더 오래 걸리면 Visual Studio Code에서 디바이스 코드를 제공하여 Microsoft 인증 웹 사이트를 통해 로그인하라는 메시지를 표시합니다. 코드를 사용하여 대신 로그인하려면 **디바이스 코드 사용**을 선택합니다.

      ![디바이스 코드를 대신 사용하여 계속](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. 코드를 복사하려면 **복사 후 열기**를 선택합니다.

      ![Azure 로그인 코드 복사](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. 새 브라우저 창을 열고 인증 웹 사이트로 이동하려면 **링크 열기**를 선택합니다.

      ![브라우저 열기 및 인증 웹 사이트로 이동 확인](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. **계정에 로그인** 페이지에서 인증 코드를 입력하고, **다음**을 선택합니다.

      ![Azure 로그인용 인증 코드 입력](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Azure 계정을 선택합니다. 로그인한 후 브라우저를 닫고 Visual Studio Code로 돌아갈 수 있습니다.

   이제 Azure 창의 **Logic Apps** 및 **통합 계정** 섹션에서 계정과 연결된 Azure 구독이 표시됩니다. 그러나 원하는 구독이 표시되지 않거나 섹션에서 너무 많은 구독이 표시되는 경우 다음 단계를 수행합니다.

   1. 포인터를 **Logic Apps** 레이블 위로 이동합니다. 도구 모음이 표시되면 **구독 선택**(필터 아이콘)을 선택합니다.

      ![Azure 구독 찾기 또는 필터링](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. 표시되는 목록에서 표시하려는 구독을 선택합니다.

1. **Logic Apps** 아래에서 원하는 구독을 선택합니다. 구독 노드가 펼쳐지고 해당 구독에 있는 모든 논리 앱이 표시됩니다.

   ![Azure 구독 선택](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > **통합 계정** 아래에서 구독을 선택하면 해당 구독에 있는 모든 통합 계정이 표시됩니다.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>새 논리 앱 만들기

1. Visual Studio Code 내에서 아직 Azure 계정 및 구독에 로그인하지 않은 경우 [이전 단계에 따라 지금 로그인합니다](#sign-in-azure).

1. Visual Studio Code의 **Logic Apps** 아래에서 구독의 바로 가기 메뉴를 열고 **로그 앱 만들기**를 선택합니다.

   ![구독 메뉴에서 "논리 앱 만들기" 선택](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   표시되는 목록에서 구독의 모든 Azure 리소스 그룹이 표시됩니다.

1. 리소스 그룹 목록에서 **새 리소스 그룹 만들기** 또는 기존 리소스 그룹을 선택합니다. 여기서는 새 리소스 그룹을 만듭니다.

   ![새 Azure 리소스 그룹 만들기](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Azure 리소스 그룹의 이름을 제공하고, Enter 키를 누릅니다.

   ![Azure 리소스 그룹에 대한 이름을 입력합니다.](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. 논리 앱의 메타데이터를 저장하려는 Azure 지역을 선택합니다.

   ![논리 앱 메타데이터를 저장하는 데 사용할 Azure 위치를 선택합니다.](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. 논리 앱의 이름을 제공하고, Enter 키를 누릅니다.

   ![논리 앱에 사용할 이름을 입력합니다.](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Azure 창의 Azure 구독 아래에서 새 빈 논리 앱이 표시됩니다. 또한 Visual Studio Code에서는 논리 앱에 대한 기본 워크플로 정의가 포함된 JSON(.logicapp.json) 파일이 열립니다. 이제 이 JSON 파일에서 논리 앱의 워크플로 정의를 수동으로 작성할 수 있습니다. 워크플로 정의의 구조 및 구문에 대한 기술 참조는 [Azure Logic Apps의 워크플로 정의 언어 스키마](../logic-apps/logic-apps-workflow-definition-language.md)를 참조하세요.

   ![빈 논리 앱 워크플로 정의 JSON 파일](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   예를 들어 RSS 트리거 및 Office 365 Outlook 작업으로 시작하는 논리 앱 워크플로 정의 샘플은 다음과 같습니다. 일반적으로 JSON 요소는 각 섹션에서 사전순으로 표시됩니다. 그러나 이 샘플은 이러한 요소들을 로직 앱의 단계가 설계자에 나타나는 순서대로 대략적으로 보여줍니다.

   > [!IMPORTANT]
   > 이 논리 앱 정의 샘플을 다시 사용하려면 Office 365 조직 계정 (예 : @fabrikam.com)이 필요합니다. 가상 이메일 주소를 사용자 고유의 이메일 주소로 바꾸어야 합니다. Outlook.com 또는 Gmail과 같은 다른 이메일 커넥터를 사용하려면 `Send_an_email_action` 작업을 [Azure Logic Apps에서 지원하는 이메일 커넥터](../connectors/apis-list.md)에서 사용할 수 있는 비슷한 작업으로 바꿉니다.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. 완료되면 논리 앱의 워크플로 정의를 저장합니다. (파일 메뉴 > 저장 또는 Ctrl+S 누르기)

1. 논리 앱을 Azure 구독에 업로드하라는 메시지가 표시되면 **업로드**를 선택합니다.

   이 단계에서는 논리 앱을 [Azure Portal](https://portal.azure.com)에 게시합니다. 그러면 논리가 활성 상태가 되어 Azure에서 실행될 수 있습니다.

   ![새 논리 앱을 Azure 구독에 업로드](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>디자이너에서 논리 앱 보기

Visual Studio Code에서는 논리 앱을 읽기 전용 디자인 보기에서 열 수 있습니다. 디자이너에서 논리 앱을 편집할 수는 없지만 디자이너 보기를 사용하여 논리 앱의 워크플로를 시각적으로 확인할 수 있습니다.

Azure 창의 **Logic Apps** 아래에서 논리 앱의 바로 가기 메뉴를 열고 **디자이너에서 열기**를 선택합니다.

읽기 전용 디자이너가 별도의 창에서 열려 논리 앱의 워크플로가 표시됩니다. 예를 들어 다음과 같습니다.

![읽기 전용 디자이너에서 논리 앱 보기](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Azure Portal에서 보기

Azure Portal에서 논리 앱을 검토하려면 다음 단계를 수행합니다.

1. 논리 앱과 연결된 동일한 Azure 계정 및 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal의 검색 상자에서 논리 앱의 이름을 입력합니다. 결과 목록에서 논리 앱을 선택합니다.

   ![Azure Portal의 새 논리 앱](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>논리 앱 사용 또는 사용 안 함

Visual Studio Code에서는 게시된 논리 앱을 편집하고 변경 내용을 저장하는 경우 이미 배포된 앱을 *덮어씁니다*. 프로덕션에서 논리 앱이 중단되지 않도록 방지하고 중단을 최소화하려면 먼저 논리 앱을 비활성화합니다. 그런 다음, 논리 앱이 여전히 작동하는지 확인한 후에 논리 앱을 다시 활성화할 수 있습니다.

1. Visual Studio Code 내에서 아직 Azure 계정 및 구독에 로그인하지 않은 경우 [이전 단계에 따라 지금 로그인합니다](#sign-in-azure).

1. 해당 구독의 모든 논리 앱을 볼 수 있도록 Azure 창의 **Logic Apps** 아래에서 Azure 구독을 펼칩니다.

   1. 원하는 논리 앱을 사용하지 않도록 설정하려면 논리 앱의 메뉴를 열고 **사용 안 함**을 선택합니다.

      ![논리 앱 사용 안 함](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. 논리 앱을 다시 활성화할 준비가 되면 논리 앱의 메뉴를 열고 **사용**을 선택합니다.

      ![논리 앱 사용](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>배포된 논리 앱 편집

Visual Studio Code에서는 이미 배포된 Azure 논리 앱의 워크플로 정의를 열고 편집할 수 있습니다.

> [!IMPORTANT] 
> 프로덕션에서 활발하게 실행되는 논리 앱을 편집하려면 먼저 [논리 앱을 사용하지 않도록 설정](#disable-enable-logic-app)하여 해당 논리 앱을 중단시키는 위험을 피하고 중단을 최소화해야 합니다.

1. Visual Studio Code 내에서 아직 Azure 계정 및 구독에 로그인하지 않은 경우 [이전 단계에 따라 지금 로그인합니다](#sign-in-azure).

1. Azure 창의 **Logic Apps** 아래에서 Azure 구독을 확장하고 원하는 논리 앱을 선택합니다.

1. 논리 앱의 메뉴를 열고, **편집기에서 열기**를 선택합니다. 또는 논리 앱의 이름 옆에 있는 편집 아이콘을 선택합니다.

   ![기존 논리 앱에 대한 편집기 열기](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code에서는 논리 앱의 워크플로 정의를 볼 수 있도록 로컬 임시 폴더에서 .logicapp.json 파일을 엽니다.

   ![게시된 논리 앱의 워크플로 정의 보기](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. 논리 앱의 워크플로 정의를 변경합니다.

1. 완료되면 변경 사항을 저장합니다. (파일 메뉴 > 저장 또는 Ctrl+S 누르기)

1. Azure Portal에서 변경 내용을 업로드하고 기존 논리 앱을 *덮어쓰라는* 메시지가 표시되면 **업로드**를 선택합니다.

   이 단계에서는 [Azure Portal](https://portal.azure.com)에서 논리 앱에 대한 업데이트를 게시합니다.

   ![Azure에서 논리 앱 정의에 대한 편집 내용 업로드](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>다른 버전 보기 또는 승격

Visual Studio Code에서는 논리 앱의 이전 버전을 열고 검토할 수 있습니다. 또한 이전 버전을 현재 버전으로 승격할 수도 있습니다.

> [!IMPORTANT] 
> 프로덕션에서 활발하게 실행되는 논리 앱을 변경하려면 먼저 [논리 앱을 사용하지 않도록 설정](#disable-enable-logic-app)하여 해당 논리 앱을 중단시키는 위험을 피하고 중단을 최소화해야 합니다.

1. 해당 구독의 모든 논리 앱을 볼 수 있도록 Azure 창의 **Logic Apps** 아래에서 Azure 구독을 펼칩니다.

1. 구독 아래에서 논리 앱, **버전**을 차례로 펼칩니다.

   **버전** 목록에서 논리 앱의 이전 버전(있는 경우)이 표시됩니다.

   ![논리 앱의 이전 버전](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. 이전 버전을 보려면 다음 단계 중 하나를 선택합니다.

   * JSON 정의를 보려면 **버전** 아래에서 해당 정의의 버전 번호를 선택합니다. 또는 해당 버전의 바로 가기 메뉴를 열고 **편집기에서 열기**를 선택합니다.

     로컬 컴퓨터에서 새 파일이 열리고 해당 버전의 JSON 정의가 표시됩니다.

   * 읽기 전용 디자이너 보기에서 버전을 보려면 해당 버전의 바로 가기 메뉴를 열고 **디자이너에서 열기**를 선택합니다.

1. 이전 버전을 현재 버전으로 승격하려면 다음 단계를 수행합니다.

   1. **버전** 아래에서 이전 버전의 바로 가기 메뉴를 열고 **승격**을 선택합니다.

      ![이전 버전 승격](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Visual Studio Code에서 확인을 요청하는 메시지가 표시된 후 계속하려면 **예**를 선택합니다.

      ![이전 버전 승격 확인](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code는 선택한 버전을 현재 버전으로 승격하고 새 번호를 승격된 버전에 할당합니다. 이제 이전의 현재 버전이 승격된 버전 아래에 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)