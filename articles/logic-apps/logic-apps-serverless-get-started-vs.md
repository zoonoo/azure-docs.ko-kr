---
title: Visual Studio에서 첫 번째 서버 리스 앱 빌드
description: Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용 하 여 서버 리스 앱 빌드, 배포 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75981157"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용 하 여 첫 번째 서버 리스 앱 빌드

Azure에서 서버를 사용 하지 않는 도구 및 기능 (예: [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Azure Functions](../azure-functions/functions-overview.md))을 사용 하 여 클라우드 앱을 신속 하 게 개발 하 고 배포할 수 있습니다. 이 문서에서는 Visual Studio에서 Azure 함수를 호출하는 논리 앱을 사용하는 서버리스 앱의 빌드를 시작하는 방법을 보여 줍니다. Azure의 서버리스 솔루션에 대한 자세한 내용은 [Functions 및 Logic Apps가 있는 Azure Serverless](../logic-apps/logic-apps-serverless-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Visual Studio에서 서버를 사용 하지 않는 응용 프로그램을 빌드하려면 다음이 필요 합니다.

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 다음 도구가 있습니다. 아직 설치 하지 않은 경우 다운로드 하 여 설치 합니다.

  * [Visual Studio 2019, 2017 또는 2015 (커뮤니티 또는 기타 버전)](https://aka.ms/download-visual-studio). 
  이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때는 **Azure 개발** 워크로드를 선택해야 합니다.

  * [MICROSOFT AZURE SDK for .net (버전 2.9.1 이상)](https://azure.microsoft.com/downloads/). 
  [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)에 대해 자세히 알아보세요.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * 원하는 Visual Studio 버전용 Azure Logic Apps 도구:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Visual Studio Marketplace에서 Azure Logic Apps 도구를 직접 다운로드 하 여 설치 하거나 [Visual Studio 내에서이 확장을 설치 하는 방법을](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)배울 수 있습니다. 
    설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

  * 로컬로 디버깅 하는 함수에 대 한 [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 입니다.

* 포함 된 논리 앱 디자이너를 사용 하는 동안 웹에 액세스할 수 있습니다.

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다. 
  예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

## <a name="create-a-resource-group-project"></a>리소스 그룹 프로젝트 만들기

시작하려면 서버리스 앱에 대한 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 만듭니다. Azure에서 단일 자산으로 전체 앱에 대 한 리소스를 구성, 관리 및 배포 하는 데 사용 하는 논리적 컬렉션인 *리소스 그룹*내에 리소스를 만듭니다. Azure에 있는 서버리스 앱의 경우 리소스 그룹에는 Azure Logic Apps 및 Azure Functions용 리소스가 포함되어 있습니다. [Azure 리소스 그룹 및 리소스](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.

1. Visual Studio를 시작 하 고 Azure 계정을 사용 하 여 로그인 합니다.

1. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

   ![Visual Studio에서 새 프로젝트 만들기](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **설치됨** 아래에서 **Visual C#** 또는 **Visual Basic**을 선택합니다. 그런 다음 **클라우드**  >  **Azure 리소스 그룹**을 선택 합니다.

   > [!NOTE]
   > **클라우드** 범주 또는 **azure 리소스 그룹** 프로젝트가 없는 경우 Azure SDK for Visual Studio를 설치 했는지 확인 합니다.

   Visual Studio 2019를 사용하는 경우 다음 단계를 따르세요.

   1. **새 프로젝트 만들기** 상자에서 Visual c # 또는 Visual Basic에 대 한 **Azure 리소스 그룹** 프로젝트 템플릿을 선택 하 고 **다음**을 선택 합니다.

   1. Azure 리소스 그룹에 사용할 이름 및 기타 프로젝트 정보를 제공 합니다. 완료되면 **만들기**를 선택합니다.

1. 프로젝트 이름과 위치를 지정한 다음 **확인**을 선택 합니다.

   Visual Studio는 템플릿 목록에서 템플릿을 선택 하 라는 메시지를 표시 합니다. 
   이 예제에서는 Azure 빠른 시작 템플릿을 사용 하 여 논리 앱과 Azure function 호출을 포함 하는 서버 리스 앱을 빌드할 수 있습니다.

   > [!TIP]
   > Azure 리소스 그룹에 솔루션을 predeploy 하지 않으려는 시나리오에서는 **빈 논리 앱 템플릿을 사용** 하 여 빈 논리 앱을 만들 수 있습니다.

1. **이 위치에서 템플릿 표시** 목록에서 **Azure 빠른 시작 (github.com/Azure/azure-quickstart-templates)** 을 선택 합니다.

1. 검색 상자에서 "논리 앱"을 필터로 입력 합니다. 결과에서 **101-논리 앱** -앱 템플릿을 선택 합니다.

   ![Azure 빠른 시작 템플릿 선택](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio에서 리소스 그룹 프로젝트용 솔루션을 만들고 엽니다. 
   선택한 Azure 빠른 시작 템플릿은 리소스 그룹 프로젝트 내에 azuredeploy.js이라는 배포 템플릿을 만듭니다. 이 배포 템플릿에는 HTTP 요청에 의해 트리거되고 Azure 함수를 호출 하 고 결과를 HTTP 응답으로 반환 하는 간단한 논리 앱에 대 한 정의가 포함 되어 있습니다.

   ![새 서버리스 솔루션](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 다음으로 Azure에 솔루션을 배포 합니다. 배포 템플릿을 열고 서버 리스 앱에 대 한 리소스를 검토 하려면 먼저이 작업을 수행 해야 합니다.

## <a name="deploy-your-solution"></a>솔루션 배포

Visual Studio의 논리 앱 디자이너에서 논리 앱을 열려면 Azure에 이미 배포 된 Azure 리소스 그룹이 있어야 합니다. 그러면 디자이너에서 리소스 및 서비스에 대한 연결을 논리 앱에 만들 수 있습니다. 이 작업의 경우 다음 단계에 따라 Visual Studio에서 Azure Portal 솔루션을 배포 합니다.

1. 솔루션 탐색기의 리소스 프로젝트 바로 가기 메뉴에서 새로 **배포**를 선택  >  **New**합니다.

   ![리소스 그룹에 대한 새 배포 만들기](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 아직 선택 하지 않은 경우 Azure 구독 및 배포 하려는 리소스 그룹을 선택 합니다. 그런 다음 **배포**를 선택 합니다.

   ![배포 설정](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. **매개 변수 편집** 상자가 나타나면 배포 시 논리 앱과 Azure 함수 앱에 사용할 리소스 이름을 제공한 다음 설정을 저장 합니다. 함수 앱에 대해 전역적으로 고유한 이름을 사용해야 합니다.

   ![논리 앱 및 함수 앱에 대한 이름 제공](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio에서 지정한 리소스 그룹에 대한 배포를 시작하면 솔루션의 배포 상태가 Visual Studio **출력** 창에 표시됩니다. 
   배포가 완료되면 Azure Portal에서 해당 논리 앱이 활성 상태로 있습니다.

## <a name="edit-your-logic-app-in-visual-studio"></a>Visual Studio에서 논리 앱 편집

배포 후 논리 앱을 편집 하려면 Visual Studio에서 논리 앱 디자이너를 사용 하 여 논리 앱을 엽니다.

1. 솔루션 탐색기에서 azuredeploy.js파일의 바로 가기 메뉴에서 **논리 앱 디자이너를 사용 하 여 열기**를 선택 합니다.

   ![논리 앱 디자이너에서 azuredeploy.js열기](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

1. **논리 앱 속성** 상자가 표시 되 면 **구독**에서 Azure 구독을 선택 합니다 (아직 선택 하지 않은 경우). **리소스 그룹**에서 솔루션을 배포한 리소스 그룹 및 위치를 선택 하 고 **확인**을 선택 합니다.

   ![논리 앱 속성](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Logic Apps 디자이너가 열리면 단계를 계속 추가하거나 워크플로를 변경하고 업데이트를 저장할 수 있습니다.

   ![Logic Apps 디자이너에서 열린 논리 앱](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Azure Functions 프로젝트 만들기

JavaScript, Python, F #, PowerShell, 일괄 처리 또는 Bash를 사용 하 여 함수 프로젝트 및 함수를 만들려면 [Azure Functions Core Tools 작업](../azure-functions/functions-run-local.md)의 단계를 따르세요. 솔루션 내에서 c #을 사용 하 여 Azure 함수를 개발 하려면 [.net 클래스 라이브러리를 함수 앱로 게시](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)의 단계를 수행 하 여 c # 클래스 라이브러리를 사용 합니다.

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio에서 함수 배포

배포 템플릿은 파일의 azuredeploy.js변수에 지정 된 Git 리포지토리에서 솔루션에 있는 모든 Azure 함수를 배포 합니다. 솔루션에서 함수 프로젝트를 만들고 작성 하는 경우 프로젝트를 Git 소스 제어 (예: GitHub 또는 Azure DevOps)에 체크 인 한 다음 `repo` 템플릿이 Azure 함수를 배포 하도록 변수를 업데이트할 수 있습니다.

## <a name="manage-logic-apps-and-view-run-history"></a>논리 앱 관리 및 실행 기록 보기

Azure에 이미 배포 된 논리 앱의 경우에도 Visual Studio에서 해당 앱을 편집 하 고, 관리 하 고, 실행 기록을 보고, 사용 하지 않도록 설정할 수 있습니다.

1. Visual Studio의 **보기** 메뉴에서 **클라우드 탐색기**를 엽니다.

1. **모든 구독**에서 관리 하려는 논리 앱과 연결 된 Azure 구독을 선택 하 고 **적용**을 선택 합니다.

1. **Logic Apps** 아래에서 논리 앱을 선택합니다. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

이제 이미 게시된 논리 앱을 리소스 그룹 프로젝트에 다운로드할 수 있습니다. 따라서 Azure Portal에서 논리 앱을 시작할 수 있지만 Visual Studio에서 해당 앱을 계속 가져오고 관리할 수 있습니다. 자세한 내용은 [Visual Studio로 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 논리 앱 관리](manage-logic-apps-with-visual-studio.md)
