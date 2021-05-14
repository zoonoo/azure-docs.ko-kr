---
title: 흐름을 Power Automate에서 Azure Logic Apps으로 내보내기
description: Azure Resource Manager 템플릿 형식으로 내보내기를 통하여 흐름을 Power Automate에서 Azure Logic Apps으로 마이그레이션 하기
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699099"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Power Automate에서 흐름을 내보내고 Azure Logic Apps에 배포

흐름의 기능을 연장하고 확장하기 위하여 해당 흐름을 [Power Automate](https://flow.microsoft.com)에서 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 마이그레이션할 수 있습니다. 흐름을 논리 앱용 Azure Resource Manager 템플릿으로 가져온 뒤, 해당 논리 앱 템플릿을 Azure 리소스 그룹에 배포한 다음 논리 앱 디자이너에서 해당 논리 앱을 열 수 있습니다.

> [!IMPORTANT]
> 2020년 8월 이후 만들어진 Power Automate 흐름은 Logic Apps로 내보낼 수 없습니다. 2020년 10월에 Power Automate는 [OpenAPI 2.0 protocol](https://swagger.io/specification/v2/)에서 새로운 흐름 생성을 표준화하였습니다. 해당 프로토콜을 기반으로 만들어진 새 흐름은 Logic Apps 워크플로와 호환되지 않기 때문에 해당 흐름은 논리 앱에 내보낼 수 없습니다. 대신, 해당 흐름에 대하여서는 직접 [로직 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 실행하여야 합니다.

Power Automate 커넥터를 모두 Azure Logic Apps에서 사용할 수 있는 것은 아닙니다. Azure Logic Apps와 동일한 커넥터가 있는 Power Automate 흐름만 마이그레이션할 수 있습니다. 예를 들면, 단추 트리거, 승인 트리거, 알람 커넥터는 Power Automate에만 적용됩니다. 

* 해당되는 Logic Apps를 갖추지 못한 Power Automate 커넥터를 파악하려면 [Power Automate 커넥터](/connectors/connector-reference/connector-reference-powerautomate-connectors)를 확인하세요.

* 해당되는 Power Automate를 갖추지 못한 Logic Apps 커넥터를 파악하려면 [Logic Apps 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)를 확인하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Power Automate에서 내보내려는 흐름

## <a name="export-your-flow"></a>흐름 내보내기

1. [Power Automate](https://flow.microsoft.com)에 로그인 하여 **내 흐름** 을 선택합니다. 플로우를 찾아 선택합니다. 도구 모음에서 말줄임표( **...** ) 단추> **내보내기** > **Logic Apps 템플릿(.json)** 을 선택합니다.

   ![Power Automate에서 흐름 내보내기](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 원하는 위치에 템플릿의 .json 파일을 저장합니다.

자세한 내용은 [Azure Logic Apps로 성장](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)을 참조하세요.

## <a name="deploy-template-by-using-the-azure-portal"></a>Azure Portal을 통하여 템플릿 배포하기

1. Azure 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 홈페이지의 검색 상자에 `custom template`을 입력합니다. 결과에서 **사용자 지정 템플릿 배포** > **만들기** 를 선택합니다.

   ![“템플릿 배포”를 찾아 선택합니다.](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. **사용자 지정 배포** 에서 **편집기에 나만의 템플릿 빌드하기** 를 선택합니다.

   ![“편집기에 나만의 템플릿 빌드하기”를 선택하기](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. **템플릿 편집** 도구 모음에서 **파일 로드** 를 선택합니다.

   ![“파일 로드” 선택하기](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Power Automate에서 내보낸 JSON 템플릿 파일을 저장하였던 위치로 이동합니다. 템플릿 파일 > **열기** 를 선택합니다.

1. 편집기가 템플릿에 JSON, 매개 변수, 리소스를 표시하면 **저장** 을 선택합니다.

   ![템플릿 저장](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 이제 논리 앱에 자세한 정보를 제공합니다.

   1. 템플릿에 대하여 입력 매개 변숫값을 선택 또는 지정합니다.

      | 속성 | Description |
      |----------|-------------|
      | **구독** | 청구에 사용할 Azure 구독 |
      | **리소스 그룹** | 논리 앱에 사용할 Azure 리소스 그룹 기존 그룹을 사용하거나 새 그룹을 만들 수 있습니다. |
      | **위치** | 새 리소스 그룹을 만들 경우 사용할 Azure 지역 |
      | **논리 앱 이름** | 논리 앱 리소스에 사용할 이름 |
      | **논리 앱 위치** | 논리 앱 리소스를 만들고자 하는 위치가 Azure 리소스 그룹이 아닌 경우의 Azure 지역 |
      | <*connection-name*> | 논리 앱에서 다시 사용할 수 있는, 이전에 만든 연결에 대한 하나 또는 여러 개의 이름 <p><p>**참고**: 이번이 처음으로 만든 논리 앱이라면 연결이 모두 새로 생성되므로 기본 이름을 사용할 수 있습니다. 그렇지 않은 경우라면 여러 논리 앱에서 사용할 수 있는 이전에 만들었던 연결의 이름을 지정할 수 있습니다. |
      |||

      예:

      ![템플릿용 입력 매개 변수 지정하기](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. 완료하였으면 필요한 Azure 리소스 만들기와 이에 따른 Azure 구독 관련 청구에 대한 **사용 약관** 을 검토하세요.

   1. 준비가 되면 **위에 명시된 사용 약관에 동의합니다** > **구매** 를 선택합니다.

      Azure는 지정한 리소스 그룹에 대한 논리 앱으로서 템플릿을 배포합니다.

1. Power Automate에서 마이그레이션하는 논리 앱은 모두 사용하지 않음 상태로 배포됩니다. 논리 앱을 사용하도록 설정하기 전에 다음 단계에 따라 새로운 연결 일체에 대한 권한을 부여합니다.

   1. Azure Portal에서 직접 만든 논리 앱을 엽니다. 논리 앱 메뉴에서 **논리 앱 디자이너** 를 엽니다.

      권한 부여가 필요한 연결마다 경고 아이콘이 표시됩니다.

      ![경고 아이콘](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 권한이 부여된 연결이 필요한 단계 하나하나를 확장한 뒤 **새로 추가** 를 선택합니다.

      ![Outlook “연결” 창에서 “새로 추가” 단추를 선택한 상태를 보여 주는 스크린샷](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 각각의 서비스에 로그인하거나 필요한 자격 증명을 제공하여 연결 권한을 부여합니다.

   1. 연결을 업데이트한 후에는 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 활성화할 준비가 되면 **개요** 를 선택한 뒤, **사용** 을 선택합니다.

   ![논리 앱 사용하기](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 중복되는 워크플로 실행을 방지하기 위하여 반드시 원래 흐름은 비활성화하거나 삭제하세요.

## <a name="deploy-template-by-using-visual-studio"></a>Visual Studio를 사용하여 템플릿 배포하기

논리 앱을 만들기 위하여 Visual Studio를 [필수 구성 요소](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)로 설정하였다면 내보낸 템플릿을 Visual Studio를 통하여 Azure Logic Apps에 배포할 수 있습니다.

1. Power Automate에서 내보낸 논리앱에 대한 .json 파일을 Visual Studio에서 찾아 엽니다.

1. Visual Studio에서 [빠른 시작: Azure Logic Apps로 자동화된 작업, 프로세스, 워크플로 만들기 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)에 나와 있는 단계에 따라 **논리 앱** 템플릿을 사용하는 **Azure 리소스 그룹** 프로젝트를 만듭니다.

   본 예제에서는 이름이 "ImportedLogicApp"인 Visual Studio 솔루션을 만듭니다.

   ![Azure 리소스 그룹 프로젝트 만들기](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 솔루션을 만든 다음, 솔루션 탐색기에서 **LogicApp.json** 파일이 열려 있지 않은 경우 해당 파일을 엽니다.

1. 내보낸 템플릿의 콘텐츠를 복사하여 **LogicApp.json** 파일에 그 내용을 덮어씌웁니다.

1. 논리 앱을 배포하기 전에 다음 단계에 따라 새로운 연결 일체에 대한 권한을 부여합니다.

   1. **LogicApp.json** 바로 가기 메뉴를 열고 **논리 앱 디자이너로 열기** 를 선택합니다.

      ![논리 앱 디자이너로 템플릿 열기](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 메시지가 표시되면 논리 앱을 배포하기 위하여 사용하려는 Azure 구독 및 리소스 그룹을 선택합니다.

      ![Azure 구독 및 리소스 그룹 선택하기](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      논리 앱이 디자이너에 표시되면 권한 부여가 필요한 모든 연결에 경고 아이콘이 표시됩니다.

      ![경고 아이콘이 표시된 연결](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 권한이 부여된 연결이 필요한 단계 하나하나를 확장한 뒤 **새로 추가** 를 선택합니다.

      ![새 연결을 추가합니다.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 각각의 서비스에 로그인하거나 필요한 자격 증명을 제공하여 연결 권한을 부여합니다.

   1. 논리 앱을 배포하기 전에 솔루션을 저장합니다.

1. 솔루션 탐색기에서 프로젝트 바로 가기 메뉴를 열고 **배포** > **새로 배포** 를 선택합니다. 메시지가 표시되면 Azure 계정으로 로그인합니다.

1. 메시지가 표시되면, 템플릿 매개 변숫값을 전달하는 데 사용할 Azure 구독과 Azure 리소스 그룹 및 기타 배포에 사용하려는 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md) 등의 설정을 확인한 다음, **배포** 를 선택합니다.

   ![배포 설정 확인하기](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. **매개 변수 편집** 상자가 나타나면, Azure의 논리 앱 리소스 이름을 입력하고 elect **저장** 을 선택합니다.  

   ![배포 매개 변수 편집](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   배포가 시작되면 Visual Studio **출력** 창에 앱의 배포 상태가 표시됩니다. 상태가 표시되지 않으면 **출력 표시** 목록을 연 다음, Azure 리소스 그룹을 선택 합니다. 예:

   ![출력 창](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   논리 앱에 입력이 필요한 연결이 있는 경우, PowerShell 창이 백그라운드에서 열리고 필요한 암호나 비밀 키를 묻는 메시지가 나타납니다. 이 정보를 입력한 후에 배포가 계속됩니다.

   ![연결 인증](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   배포 완료 후에는 논리 앱이 게시되지만 Azure Portal에서 활성화된 상태는 아닙니다.

1. Azure Portal에서 논리 앱을 활성화할 준비가 되면 논리 앱 디자이너에서 논리 앱을 찾아 엽니다. 논리 앱 메뉴에서 **개요** 를 선택한 다음 **사용** 을 선택합니다.

1. 중복되는 워크플로 실행을 방지하기 위하여 반드시 원래 흐름은 비활성화하거나 삭제하세요.

본 배포 단계에 대한 자세한 내용은 [빠른 시작: Azure Logic Apps로 자동화된 작업, 프로세스, 워크플로 만들기 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)의 자세한 내용을 알아봅니다.
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)의 자세한 내용을 알아봅니다.
