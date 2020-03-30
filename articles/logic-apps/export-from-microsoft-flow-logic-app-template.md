---
title: 전원 자동화에서 Azure 논리 앱으로의 내보내기 흐름
description: Azure 리소스 관리자 템플릿으로 내보내 전원 자동화에서 Azure 논리 앱으로 흐름 마이그레이션
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428874"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Power Automate에서 흐름을 내보내고 Azure Logic Apps에 배포

흐름의 기능을 확장하고 확장하려면 해당 흐름을 [전원 자동화에서](https://flow.microsoft.com) Azure [논리 앱으로](../logic-apps/logic-apps-overview.md)마이그레이션할 수 있습니다. 흐름을 논리 앱에 대한 Azure 리소스 관리자 템플릿으로 내보낸 다음 해당 논리 앱 템플릿을 Azure 리소스 그룹에 배포한 다음 논리 앱 디자이너에서 해당 논리 앱을 열 수 있습니다.

> [!NOTE]
> Azure 논리 앱에서 모든 전원 자동 자동화 커넥터를 사용할 수 있는 것은 아닙니다. Azure 논리 앱에서 [동등한 커넥터가](../connectors/apis-list.md) 있는 흐름을 가져올 수 있습니다. 예를 들어 단추 트리거, 승인 커넥터 및 알림 커넥터는 전원 자동화에 만 전이됩니다.
>
> Power Automate에서 내보낸 OpenAPI 기반 흐름은 현재 논리 앱 템플릿으로 배포할 때 지원되지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 전원 자동화에서 내보낼 흐름

## <a name="export-a-flow"></a>흐름 내보내기

1. [전원 자동 자동화에](https://flow.microsoft.com)로그인하고 **내 흐름을**선택합니다. 흐름을 찾아 선택합니다. 도구 모음에서 타원 **(...**) 버튼을 선택합니다. 로직 앱 **내보내기** > **템플릿(.json)을**선택합니다.

   ![흐름 내보내기](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 템플릿을 원하는 위치에 저장합니다.

자세한 내용은 [Azure 논리 앱으로 확대를](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)참조하십시오.

## <a name="deploy-template-by-using-the-azure-portal"></a>Azure 포털을 사용하여 템플릿 배포

1. Azure 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다.

1. Azure 기본 메뉴에서 **리소스 만들기**를 선택합니다. 검색 상자에 "템플릿 배포"를 입력합니다. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)를**선택한 다음 **을**선택합니다.

   !["템플릿 배포" 선택](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. **사용자 지정 배포에서** **편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.**

   !["편집기에서 사용자 고유의 템플릿 빌드"를 선택합니다.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 템플릿 **편집** 도구 모음에서 **파일 로드를**선택합니다. 전원 자동화에서 내보낸 JSON 템플릿을 찾아 선택하고 **열기를**선택합니다.

   !["파일 로드"를 선택합니다.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 편집기에서 템플릿의 JSON, 매개 변수 및 리소스가 표시되면 **저장을**선택합니다.
  
   ![템플릿 저장](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 이제 템플릿에 대해 이러한 입력 매개 변수를 지정합니다.

   * 청구에 사용할 Azure 구독
   * Azure 리소스 그룹
   * Azure 리소스 그룹의 위치
   * 논리 앱 리소스의 이름
   * Azure 리소스 그룹과 다른 경우 논리 앱 리소스의 위치
   * 논리 앱에서 다시 사용할 수 있는 이전에 만든 연결의 이름입니다.

      첫 번째 논리 앱을 만드는 경우 모든 연결이 새 연결로 만들어지므로 기본 이름을 수락할 수 있습니다. 그렇지 않으면 이전에 만든 연결의 이름을 지정할 수 있으며, 이 이름은 여러 논리 앱에서 사용할 수 있습니다.

   템플릿에 대해 이 정보를 제공한 후 필요한 Azure 리소스를 만들고 그에 따라 Azure 구독에 대한 요금을 청구하기 위해 Azure Marketplace 이용 약관을 검토하고 동의한 다음 **구입을**선택합니다.
  
   ![템플릿에 대한 입력 매개변수 지정](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure는 지정된 리소스 그룹에 논리 앱으로 템플릿을 배포합니다. Power 자동화에서 마이그레이션하는 모든 논리 앱은 비활성화된 상태로 배포됩니다.

1. 논리 앱을 활성화하기 전에 다음 단계를 수행하여 새 연결에 권한을 부여합니다.

   1. 만든 논리 앱을 엽니다. 논리 앱의 메뉴에서 **논리 앱 디자이너를**선택합니다.

      권한 부여가 필요한 각 연결에는 경고 아이콘이 표시됩니다.

      ![경고 아이콘](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 권한이 부여된 연결이 필요한 각 단계에 대해 해당 단계를 확장하고 **새 추가를**선택합니다.

      ![새 연결을 추가합니다.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 각 서비스에 로그인하거나 연결을 승인하는 데 필요한 자격 증명을 제공합니다.

1. 논리 앱을 저장합니다. 논리 앱을 활성화할 준비가 되면 논리 앱의 메뉴에서 **개요를**선택한 다음 **에서 를 사용하도록**선택합니다.

   ![논리 앱 사용](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 중복 워크플로를 실행하지 않으려면 원래 흐름을 비활성화하거나 삭제해야 합니다.

## <a name="deploy-template-by-using-visual-studio"></a>비주얼 스튜디오를 사용하여 템플릿 배포

논리 앱을 만들기 위한 전제 [조건으로](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) Visual Studio를 설정한 경우 내보낸 템플릿을 Visual Studio에서 Azure 논리 앱으로 배포할 수 있습니다.

1. Visual Studio에서 Power 자동화에서 내보낸 템플릿 파일을 엽니다.

1. Visual Studio에서 Azure 리소스 그룹 프로젝트를 만들고 빠른 시작의 단계에 따라 **논리 앱** [템플릿을 선택합니다.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

   ![Azure 리소스 그룹 프로젝트 만들기](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 솔루션 탐색기에서 파일이 아직 열려 있지 않은 경우 **LogicApp.json** 파일을 엽니다.

1. 내보낸 템플릿의 내용을 복사하고 **LogicApp.json** 파일의 내용을 덮어씁니다.

1. 논리 앱을 배포하기 전에 다음 단계를 수행하여 새 연결에 권한을 부여합니다.

   1. **LogicApp.json** 바로 가기 메뉴를 열고 **논리 앱 디자이너로 열기를**선택합니다.

      ![논리 앱 디자이너가 있는 템플릿 열기](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 메시지가 표시되면 논리 앱을 배포하는 데 사용할 Azure 구독 및 리소스 그룹을 선택합니다.

      ![Azure 구독 및 리소스 그룹 선택](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      논리 앱이 디자이너에 나타나면 권한 부여가 필요한 모든 연결에는 경고 아이콘이 표시됩니다.

      ![경고 아이콘이 있는 연결](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 권한이 부여된 연결이 필요한 각 단계에 대해 해당 단계를 확장하고 **새 추가를**선택합니다.

      ![새 연결을 추가합니다.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 각 서비스에 로그인하거나 연결을 승인하는 데 필요한 자격 증명을 제공합니다.

   1. 논리 앱을 배포하기 전에 솔루션을 저장합니다.

1. 솔루션 탐색기에서 프로젝트 바로 가기 메뉴를 열고**새** **배포를** > 선택합니다. 메시지가 표시되면 Azure 계정으로 로그인합니다.

1. 메시지가 표시되면 Azure 구독, Azure 리소스 그룹 및 배포에 사용할 기타 설정(예: 템플릿 매개 변수 값 전달에 사용할 [매개 변수 파일)을](../azure-resource-manager/templates/parameter-files.md) 확인한 다음 **배포를**선택합니다.

   ![배포 설정 확인](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 매개 **변수 편집** 상자가 나타나면 Azure에서 논리 앱 리소스의 이름을 제공하고 **저장을**선택합니다.  

   ![배포 매개 변수 편집](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   배포가 시작되면 Visual Studio **출력** 창에 앱의 배포 상태가 표시됩니다. 상태가 표시되지 않으면 **출력 표시** 목록을 연 다음, Azure 리소스 그룹을 선택 합니다. 예를 들어:

   ![출력 창](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   논리 앱의 연결이 사용자의 입력이 필요한 경우 PowerShell 창이 백그라운드에서 열리고 필요한 암호 또는 비밀 키를 묻는 메시지가 표시됩니다. 이 정보를 입력한 후에 배포가 계속됩니다.

   ![연결 인증](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   배포가 완료되면 논리 앱이 게시되지만 Azure 포털에서 활성화되지 않습니다.

1. Azure 포털에서 논리 앱을 활성화할 준비가 되면 논리 앱 디자이너에서 논리 앱을 찾아 엽니다. 논리 앱의 메뉴에서 **개요를**선택한 다음 **에서 를 선택합니다.**

1. 중복 워크플로를 실행하지 않으려면 원래 흐름을 비활성화하거나 삭제해야 합니다.

이러한 배포 단계에 대한 자세한 내용은 [빠른 시작: Azure Logic Apps - Visual Studio를 사용하여 자동화된 작업, 프로세스 및 워크플로 만들기를](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure) 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱용 커넥터에 대해](../connectors/apis-list.md) 자세히 알아보기
* [Azure 논리 앱에](../logic-apps/logic-apps-overview.md) 대해 자세히 알아보기
