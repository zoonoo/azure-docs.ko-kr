---
title: Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용 하 여 서버 리스 앱 빌드
description: 빌드, 배포 및 Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용 하 여 첫 번째 서버 리스 앱 관리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444863"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용 하 여 첫 번째 서버 리스 앱 빌드

신속 하 게 개발 하 고 사용 하 여 서버 리스 도구와 기능, Azure에서와 같은 클라우드 앱을 배포할 수 있습니다 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 하 고 [Azure Functions](../azure-functions/functions-overview.md)합니다. 이 문서에서는 Visual Studio에서 Azure 함수를 호출하는 논리 앱을 사용하는 서버리스 앱의 빌드를 시작하는 방법을 보여 줍니다. Azure의 서버리스 솔루션에 대한 자세한 내용은 [Functions 및 Logic Apps가 있는 Azure Serverless](../logic-apps/logic-apps-serverless-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Visual Studio에서 서버 리스 앱을 빌드하려면 다음을 수행 해야 합니다.

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 다음 도구입니다. 없다면 이미 다운로드 하 고 설치 합니다.

  * [Visual Studio 2019, 2017 또는 2015 (Community 또는 다른 버전)](https://aka.ms/download-visual-studio)합니다. 
  이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때는 **Azure 개발** 워크로드를 선택해야 합니다.

  * [Microsoft Azure SDK for.NET (버전 2.9.1 이상)](https://azure.microsoft.com/downloads/)합니다. 
  [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)에 대해 자세히 알아보세요.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * 원하는 Visual Studio 버전용 Azure Logic Apps 도구:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Visual Studio Marketplace에서 직접 Azure Logic Apps 도구를 다운로드해 설치하거나 [Visual Studio 내에서 이 확장을 설치하는 방법](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)을 알아볼 수 있습니다. 
    설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

  * [Azure Functions 핵심 도구](https://www.npmjs.com/package/azure-functions-core-tools) 로컬로 함수를 디버깅 합니다.

* 포함 된 논리 앱 디자이너를 사용 하는 동안 웹에 액세스 합니다.

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다. 
  예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

## <a name="create-a-resource-group-project"></a>리소스 그룹 프로젝트 만들기

시작하려면 서버리스 앱에 대한 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 만듭니다. Azure 내에서 리소스를 만듭니다는 *리소스 그룹*, 구성, 관리 및 단일 자산으로 전체 앱에 대 한 리소스 배포에 사용할 논리적 컬렉션입니다. Azure에 있는 서버리스 앱의 경우 리소스 그룹에는 Azure Logic Apps 및 Azure Functions용 리소스가 포함되어 있습니다. [Azure 리소스 그룹 및 리소스](../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.

1. Visual Studio를 시작 하 고 Azure 계정을 사용 하 여 로그인 합니다.

1. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

   ![Visual Studio에서 새 프로젝트 만들기](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **설치됨** 아래에서 **Visual C#** 또는 **Visual Basic**을 선택합니다. 그런 다음 선택 **클라우드** > **Azure 리소스 그룹**합니다.

   > [!NOTE]
   > 경우는 **클라우드** 범주 또는 **Azure 리소스 그룹** 프로젝트가 존재 하지 않는 Visual Studio 용 Azure SDK를 설치 해야 합니다.

   Visual Studio 2019를 사용하는 경우 다음 단계를 따르세요.

   1. 에 **새 프로젝트를 만듭니다** 상자를 선택 합니다 **Azure 리소스 그룹** 하거나 시각적 개체에 대 한 프로젝트 템플릿을 C# 또는 Visual Basic을 선택한 후 **다음**.

   1. 이름 및 Azure 리소스 그룹에 사용 하려는 다른 프로젝트 정보를 제공 합니다. 완료되면 **만들기**를 선택합니다.

1. 이름 및 위치에 프로젝트를 지정 하 고 선택한 **확인**합니다.

   Visual Studio 템플릿 목록에서 템플릿을 선택 하 라는 메시지를 표시 합니다. 
   이 예제는 Azure 빠른 시작 템플릿을 사용 하므로 논리 앱과 Azure 함수에 대 한 호출을 포함 하는 서버 리스 앱을 빌드할 수 있습니다.

   > [!TIP]
   > Azure 리소스 그룹에 솔루션이 predeploy 않으려는 경우 공백은 사용할 수 있습니다 **논리 앱** 템플릿을 빈 논리 앱을 만들기만 합니다.

1. **이 위치의 템플릿 표시** 목록에서 **Azure 빠른 시작 (github.com/Azure/azure-quickstart-templates)** 합니다.

1. 검색 상자에서 필터로 "논리 앱"을 입력 합니다. 결과에서 선택 합니다 **101-logic-app-and-function-app** 템플릿.

   ![Azure 빠른 시작 템플릿 선택](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio에서 리소스 그룹 프로젝트용 솔루션을 만들고 엽니다. 
   선택한 Azure 빠른 시작 템플릿 이라는 리소스 그룹 프로젝트 내에서 azuredeploy.json 배포 템플릿을 만듭니다. 이 배포 템플릿은 HTTP 요청에 의해 트리거되는 Azure 함수를 호출 하 고 HTTP 응답으로 결과 반환 하는 간단한 논리 앱에 대 한 정의 포함 합니다.

   ![새 서버리스 솔루션](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 다음으로 Azure에 솔루션을 배포 합니다. 배포 템플릿을 열려면 및 서버 리스 앱에 대 한 리소스를 검토 하기 전에이 작업을 수행 해야 합니다.

## <a name="deploy-your-solution"></a>솔루션 배포

Visual Studio에서 Logic App Designer에서 논리 앱을 열 수 있습니다, 전에 Azure에서 이미 배포 된 Azure 리소스 그룹을 해야 합니다. 그러면 디자이너에서 리소스 및 서비스에 대한 연결을 논리 앱에 만들 수 있습니다. 이 태스크에 대 한 Azure portal에 Visual Studio에서 솔루션을 배포 하려면 다음이 단계를 수행 합니다.

1. 솔루션 탐색기에서 리소스 프로젝트의 바로 가기 메뉴를 선택 **배포** > **새로 만들기**합니다.

   ![리소스 그룹에 대한 새 배포 만들기](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 선택 되어 있지 않은 경우에 Azure 구독 및 배포 하려는 리소스 그룹을 선택 합니다. 그런 다음 선택 **배포**합니다.

   ![배포 설정](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 경우는 **매개 변수 편집** 상자가 나타나면 논리 앱과 배포 시 Azure 함수 앱에 사용할 리소스 이름을 제공 하 고 설정을 저장 합니다. 함수 앱에 대해 전역적으로 고유한 이름을 사용해야 합니다.

   ![논리 앱 및 함수 앱에 대한 이름 제공](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio에서 지정한 리소스 그룹에 대한 배포를 시작하면 솔루션의 배포 상태가 Visual Studio **출력** 창에 표시됩니다. 
   배포가 완료되면 Azure Portal에서 해당 논리 앱이 활성 상태로 있습니다.

## <a name="edit-your-logic-app-in-visual-studio"></a>Visual Studio에서 논리 앱 편집

배포 후 논리 앱을 편집 하려면 Visual Studio에서 Logic App Designer를 사용 하 여 논리 앱을 엽니다.

1. 솔루션 탐색기에서 선택에 azuredeploy.json 파일의 바로 가기 메뉴에서 **사용 하 여 논리 앱 디자이너 열기**합니다.

   ![논리 앱 디자이너에서 azuredeploy.json을 엽니다](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 이 명령은 Visual Studio 2019에 없다면 Visual Studio에 대 한 최신 업데이트가 있는지 확인 합니다.

1. 후 합니다 **논리 앱 속성** 상자가 나타나면 **구독**, 선택 되어 있지 않은 경우 Azure 구독을 선택 합니다. 아래 **리소스 그룹**리소스 그룹 및 솔루션을 배포할 위치 선택 하 고 선택한 **확인**합니다.

   ![논리 앱 속성](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Logic Apps 디자이너가 열리면 단계를 계속 추가하거나 워크플로를 변경하고 업데이트를 저장할 수 있습니다.

   ![Logic Apps 디자이너에서 열린 논리 앱](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Azure Functions 프로젝트 만들기

JavaScript, Python을 사용 하 여 함수 프로젝트 및 함수를 만들려면 F#, PowerShell, Bash, 일괄 처리의 단계에 따라 [Azure Functions 핵심 도구를 사용 하 여 작동](../azure-functions/functions-run-local.md)합니다. 사용 하 여 Azure 함수를 개발 하려면 C# 솔루션 내에서 사용 하 여를 C# 의 단계를 수행 하 여 클래스 라이브러리 [함수 앱으로.NET 클래스 라이브러리에 게시](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio에서 함수 배포

배포 템플릿의 azuredeploy.json 파일에서 변수를 통해 지정 된 Git 리포지토리에서 솔루션에 있는 모든 Azure functions를 배포 합니다. Git 소스 제어 (예: GitHub 또는 Azure DevOps)에 해당 프로젝트를 확인 하 고 다음 업데이트 수를 만들고 솔루션에 Functions 프로젝트를 작성 하는 경우는 `repo` 변수 템플릿을 Azure function 배포 되도록 합니다.

## <a name="manage-logic-apps-and-view-run-history"></a>논리 앱 관리 및 실행 기록 보기

Azure에 이미 배포 된 논리 앱에 대해 계속, 관리, 실행 기록을 편집한 Visual Studio에서 해당 앱을 사용 하지 않도록 설정 합니다.

1. Visual Studio의 **보기** 메뉴에서 **클라우드 탐색기**를 엽니다.

1. 아래 **모든 구독**를 관리 하 고 클릭 하려는 논리 앱과 연결 된 Azure 구독 선택 **적용**합니다.

1. **Logic Apps** 아래에서 논리 앱을 선택합니다. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   > [!TIP]
   > 이 명령은 Visual Studio 2019에 없다면 Visual Studio에 대 한 최신 업데이트가 있는지 확인 합니다.

이제 이미 게시된 논리 앱을 리소스 그룹 프로젝트에 다운로드할 수 있습니다. 따라서 논리 앱을 Azure portal에서 시작 되었을 수도 있습니다, 있지만 여전히를 가져오고 Visual Studio에서 해당 앱을 관리 합니다. 자세한 내용은 [Visual Studio로 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 논리 앱 관리](manage-logic-apps-with-visual-studio.md)
