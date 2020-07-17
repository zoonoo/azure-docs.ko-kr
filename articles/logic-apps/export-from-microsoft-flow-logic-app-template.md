---
title: 전원 자동화에서 Azure Logic Apps로 흐름 내보내기
description: Azure Resource Manager 템플릿으로 내보내 전원 자동화에서 Azure Logic Apps로 흐름 마이그레이션
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: b8bf409d759b74e6a5ef0d840aebbe807a04448a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324863"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Power Automate에서 흐름을 내보내고 Azure Logic Apps에 배포

흐름의 기능을 확장 하 고 확장 하려면 [전원 자동화](https://flow.microsoft.com) 에서 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 흐름을 마이그레이션할 수 있습니다. 논리 앱에 대 한 Azure Resource Manager 템플릿으로 흐름을 내보내고, 해당 논리 앱 템플릿을 Azure 리소스 그룹에 배포한 다음 논리 앱 디자이너에서 해당 논리 앱을 열 수 있습니다.

> [!NOTE]
> 모든 전원 자동화 커넥터를 Azure Logic Apps에서 사용할 수 있는 것은 아닙니다. Azure Logic Apps에서 동일한 커넥터가 있는 전원 자동화 흐름과 마이그레이션할 수 있습니다. 예를 들어 단추 트리거, 승인 커넥터 및 알림 커넥터는 전원 자동화에만 적용 됩니다. 현재 전원 자동화의 OpenAPI 기반 흐름은 논리 앱 템플릿으로 내보내기 및 배포에 대해 지원 되지 않습니다.
>
> * 해당 하는 Logic Apps 없는 전원 자동화 커넥터를 찾으려면 [전원 자동화 커넥터](https://docs.microsoft.com/connectors/connector-reference/connector-reference-powerautomate-connectors)를 참조 하세요.
>
> * 전원 자동화가 해당 하는 Logic Apps 커넥터를 찾으려면 [커넥터 Logic Apps](https://docs.microsoft.com/connectors/connector-reference/connector-reference-powerautomate-connectors)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 전원 자동화에서 내보내려는 흐름

## <a name="export-your-flow"></a>흐름 내보내기

1. [Power 자동화](https://flow.microsoft.com)에 로그인 하 고 **내 흐름**을 선택 합니다. 흐름을 찾아 선택 합니다. 도구 모음에서 줄임표 (**...**) 단추를 선택 하 **Export**  >  **Logic Apps 템플릿 (json)**> 내보냅니다.

   ![전원 자동화에서 흐름 내보내기](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 템플릿의 json 파일을 원하는 위치에 저장 합니다.

자세한 내용은 [최대 Azure Logic Apps까지 증가](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)를 참조 하세요.

## <a name="deploy-template-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 템플릿 배포

1. Azure 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. Azure 홈 페이지의 검색 상자에을 입력 `custom template` 합니다. 결과에서 **사용자 지정 템플릿**  >  **만들기**배포를 선택 합니다.

   !["템플릿 배포"를 찾아 선택 합니다.](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. **사용자 지정 배포**에서 **편집기에서 고유한 템플릿 빌드**를 선택 합니다.

   !["편집기에서 사용자 고유의 템플릿 빌드"를 선택 합니다.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. **템플릿 편집** 도구 모음에서 **파일 로드**를 선택 합니다.

   !["파일 로드"를 선택 합니다.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 전원 자동화에서 내보낸 JSON 템플릿 파일을 저장 한 위치로 이동 합니다. **열**> 템플릿 파일을 선택 합니다.

1. 편집기에서 템플릿의 JSON, 매개 변수 및 리소스를 표시 한 후 **저장**을 선택 합니다.

   ![템플릿 저장](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 이제 논리 앱에 더 많은 정보를 제공 합니다.

   1. 템플릿에 대 한 입력 매개 변수 값을 선택 하거나 지정 합니다.

      | 속성 | Description |
      |----------|-------------|
      | **구독** | 청구에 사용할 Azure 구독입니다. |
      | **리소스 그룹** | 논리 앱에 사용할 Azure 리소스 그룹입니다. 기존 그룹을 사용 하거나 새 그룹을 만들 수 있습니다. |
      | **위치** | 새 리소스 그룹을 만드는 경우 사용할 Azure 지역입니다. |
      | **논리 앱 이름** | 논리 앱 리소스에 사용할 이름입니다. |
      | **논리 앱 위치** | Azure 리소스 그룹과 다른 경우 논리 앱 리소스를 만들려는 Azure 지역 |
      | <*연결-이름*> | 논리 앱에서 다시 사용할 수 있는 이전에 만든 연결에 대 한 하나 또는 여러 이름 <p><p>**참고**:이 논리 앱이 첫 번째 이면 모든 연결이 새로 만들어지므로 기본 이름을 그대로 사용할 수 있습니다. 그렇지 않은 경우에는 여러 논리 앱에서 사용할 수 있는 이전에 만든 연결의 이름을 지정할 수 있습니다. |
      |||

      예를 들어:

      ![템플릿에 대 한 입력 매개 변수 지정](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. 완료 되 면 필요한 Azure 리소스를 만들고 적절 하 게 Azure 구독을 청구 하는 데 대 한 사용 **약관** 을 검토 합니다.

   1. 준비가 되 면 **귀하가 위에 명시 된 사용 약관에 동의 함**을 선택  >  **Purchase**합니다.

      Azure는 지정 된 리소스 그룹에 템플릿을 논리 앱으로 배포 합니다.

1. 전원 자동화에서 마이그레이션하는 모든 논리 앱은 사용 안 함 상태로 배포 됩니다. 논리 앱을 사용 하도록 설정 하기 전에 다음 단계를 수행 하 여 새 연결에 대 한 권한을 부여 합니다.

   1. Azure Portal에서 사용자가 만든 논리 앱을 엽니다. 논리 앱의 메뉴에서 **논리 앱 디자이너**를 선택 합니다.

      권한 부여가 필요한 각 연결에는 경고 아이콘이 표시 됩니다.

      ![경고 아이콘](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 권한 있는 연결이 필요한 각 단계에 대해 해당 단계를 확장 하 고 **새로 추가**를 선택 합니다.

      ![새 연결을 추가합니다.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 각 서비스에 로그인 하거나 연결 권한을 부여 하는 데 필요한 자격 증명을 제공 합니다.

   1. 연결을 업데이트 한 후 디자이너 도구 모음에서 **저장**을 선택 합니다.

1. 논리 앱을 활성화할 준비가 되 면 논리 앱 메뉴에서 **개요**를 선택 하 고 **사용**을 선택 합니다.

   ![논리 앱 사용](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 중복 워크플로를 실행 하지 않으려면 원래 흐름을 비활성화 하거나 삭제 해야 합니다.

## <a name="deploy-template-by-using-visual-studio"></a>Visual Studio를 사용 하 여 템플릿 배포

논리 앱을 만들기 위한 [필수 구성 요소](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) 를 사용 하 여 visual studio를 설정한 경우 visual studio를 사용 하 여 Azure Logic Apps에 내보낸 템플릿을 배포할 수 있습니다.

1. Visual Studio에서 Power 자동화에서 내보낸 논리 앱 템플릿에 대 한 json 파일을 찾아 엽니다.

1. Visual Studio에서 [빠른 시작: Azure Logic Apps 사용 하 여 자동화 된 작업, 프로세스 및 워크플로 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)의 단계에 따라 **논리 앱** 템플릿을 사용 하는 **Azure 리소스 그룹** 프로젝트를 만듭니다.

   이 예제에서는 이름이 "ImportedLogicApp" 인 Visual Studio 솔루션을 만듭니다.

   ![Azure 리소스 그룹 프로젝트 만들기](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 솔루션이 만들어진 후 솔루션 탐색기에서 파일이 아직 열려 있지 않은 경우 파일 **에서LogicApp.js** 를 엽니다.

1. 내보낸 템플릿에서 콘텐츠를 복사 하 고 파일 **LogicApp.js** 의 내용을 덮어씁니다.

1. 논리 앱을 배포 하기 전에 다음 단계를 수행 하 여 새 연결에 대 한 권한을 부여 합니다.

   1. 바로 가기 메뉴 **에서LogicApp.js** 를 연 다음 **논리 앱 디자이너를 사용 하 여 열기**를 선택 합니다.

      ![논리 앱 디자이너를 사용 하 여 템플릿 열기](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 메시지가 표시 되 면 논리 앱을 배포 하는 데 사용 하려는 Azure 구독 및 리소스 그룹을 선택 합니다.

      ![Azure 구독 및 리소스 그룹 선택](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      논리 앱이 디자이너에 표시 되 면 권한 부여가 필요한 모든 연결에 경고 아이콘이 표시 됩니다.

      ![경고 아이콘이 있는 연결](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 권한 있는 연결이 필요한 각 단계에 대해 해당 단계를 확장 하 고 **새로 추가**를 선택 합니다.

      ![새 연결을 추가합니다.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 각 서비스에 로그인 하거나 연결 권한을 부여 하는 데 필요한 자격 증명을 제공 합니다.

   1. 논리 앱을 배포 하기 전에 솔루션을 저장 합니다.

1. 솔루션 탐색기에서 프로젝트 바로 가기 메뉴를 열고 새로 **배포**를 선택  >  **New**합니다. 메시지가 표시되면 Azure 계정으로 로그인합니다.

1. 메시지가 표시 되 면 Azure 구독, Azure 리소스 그룹 및 배포에 사용 하려는 기타 설정 (예: 템플릿 매개 변수 값을 전달 하는 데 사용할 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md) )을 확인 한 후 **배포**를 선택 합니다.

   ![배포 설정 확인](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. **매개 변수 편집** 상자가 나타나면 Azure에서 논리 앱 리소스의 이름을 입력 하 고 **저장**을 선택 합니다.  

   ![배포 매개 변수 편집](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   배포가 시작되면 Visual Studio **출력** 창에 앱의 배포 상태가 표시됩니다. 상태가 표시되지 않으면 **출력 표시** 목록을 연 다음, Azure 리소스 그룹을 선택 합니다. 예를 들어:

   ![출력 창](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   논리 앱의 연결에 사용자 입력이 필요한 경우 PowerShell 창이 백그라운드에서 열리고 필요한 암호나 비밀 키를 입력 하 라는 메시지가 표시 됩니다. 이 정보를 입력한 후에 배포가 계속됩니다.

   ![연결 인증](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   배포가 완료 되 면 논리 앱이 게시 되지만 Azure Portal에서 활성화 되지 않습니다.

1. Azure Portal에서 논리 앱을 활성화할 준비가 되 면 논리 앱 디자이너에서 논리 앱을 찾아 엽니다. 논리 앱 메뉴에서 **개요**를 선택 하 고 **사용**을 선택 합니다.

1. 중복 워크플로를 실행 하지 않으려면 원래 흐름을 비활성화 하거나 삭제 해야 합니다.

이러한 배포 단계에 대 한 자세한 내용은 [빠른 시작: Azure Logic Apps 사용 하 여 자동화 된 작업, 프로세스 및 워크플로 만들기-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps 커넥터](../connectors/apis-list.md) 에 대 한 자세한 정보
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 에 대 한 자세한 정보
