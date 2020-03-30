---
title: 비주얼 스튜디오에서 첫 번째 서버리스 앱 빌드
description: Visual Studio에서 Azure 논리 앱 및 Azure 함수를 사용하여 서버없는 앱을 빌드, 배포 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981157"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio에서 Azure 논리 앱 및 Azure 함수를 사용하여 첫 번째 서버리스 앱 빌드

Azure 논리 앱 및 [Azure Functions와](../azure-functions/functions-overview.md)같은 Azure의 서버없는 도구 및 기능을 사용하여 클라우드 [앱을](../logic-apps/logic-apps-overview.md) 신속하게 개발하고 배포할 수 있습니다. 이 문서에서는 Visual Studio에서 Azure 함수를 호출하는 논리 앱을 사용하는 서버리스 앱의 빌드를 시작하는 방법을 보여 줍니다. Azure의 서버리스 솔루션에 대한 자세한 내용은 [Functions 및 Logic Apps가 있는 Azure Serverless](../logic-apps/logic-apps-serverless-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Visual Studio에서 서버없는 앱을 빌드하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 다음 도구. 아직 없는 경우 다운로드하여 설치합니다.

  * [비주얼 스튜디오 2019, 2017, 또는 2015 (커뮤니티 또는 기타 버전)](https://aka.ms/download-visual-studio). 
  이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때는 **Azure 개발** 워크로드를 선택해야 합니다.

  * [.NET에 대한 마이크로 소프트 Azure SDK (버전 2.9.1 이상)](https://azure.microsoft.com/downloads/). 
  [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)에 대해 자세히 알아보세요.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * 원하는 Visual Studio 버전용 Azure Logic Apps 도구:

    * [비주얼 스튜디오 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Visual Studio 마켓플레이스에서 직접 Azure 논리 앱 도구를 다운로드하여 설치하거나 [Visual Studio 내부에서 이 확장을 설치하는 방법을](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)알아볼 수 있습니다. 
    설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

  * [로컬 디버깅 함수에](https://www.npmjs.com/package/azure-functions-core-tools) 대 한 Azure 함수 핵심 도구입니다.

* 임베디드 논리 앱 디자이너를 사용하는 동안 웹에 액세스합니다.

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다. 
  예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

## <a name="create-a-resource-group-project"></a>리소스 그룹 프로젝트 만들기

시작하려면 서버리스 앱에 대한 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 만듭니다. Azure에서는 전체 앱에 대한 리소스를 단일 자산으로 구성, 관리 및 배포하는 데 사용하는 논리적 컬렉션인 *리소스 그룹*내에서 리소스를 만듭니다. Azure에 있는 서버리스 앱의 경우 리소스 그룹에는 Azure Logic Apps 및 Azure Functions용 리소스가 포함되어 있습니다. [Azure 리소스 그룹 및 리소스](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.

1. Azure 계정을 사용하여 Visual Studio를 시작하고 로그인합니다.

1. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

   ![Visual Studio에서 새 프로젝트 만들기](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **설치됨** 아래에서 **Visual C#** 또는 **Visual Basic**을 선택합니다. 그런 다음 **클라우드** > **Azure 리소스 그룹을**선택합니다.

   > [!NOTE]
   > **클라우드** 범주 또는 **Azure 리소스 그룹** 프로젝트가 없는 경우 Visual Studio용 Azure SDK를 설치했는지 확인합니다.

   Visual Studio 2019를 사용하는 경우 다음 단계를 따르세요.

   1. 새 **프로젝트 만들기** 상자에서 Visual C# 또는 Visual Basic에 대한 **Azure 리소스 그룹** 프로젝트 템플릿을 선택한 다음 다음 을 **선택합니다.**

   1. Azure 리소스 그룹에 사용할 이름 및 기타 프로젝트 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

1. 프로젝트에 이름과 위치를 지정한 다음 **확인을**선택합니다.

   Visual Studio는 템플릿 목록에서 템플릿을 선택하라는 메시지를 표시합니다. 
   이 예제에서는 논리 앱및 Azure 함수에 대한 호출이 포함된 서버없는 앱을 빌드할 수 있도록 Azure QuickStart 템플릿을 사용합니다.

   > [!TIP]
   > Azure 리소스 그룹에 솔루션을 미리 배포하지 않으려는 시나리오에서는 빈 **논리 앱만** 만드는 빈 논리 앱 템플릿을 사용할 수 있습니다.

1. 이 위치 목록에서 **템플릿 표시에서** **Azure QuickStart(github.com/Azure/azure-quickstart-templates)를**선택합니다.

1. 검색 상자에 필터로 "논리 앱"을 입력합니다. 결과에서 **101 논리 앱 및 함수 앱** 템플릿을 선택합니다.

   ![Azure 빠른 시작 템플릿 선택](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio에서 리소스 그룹 프로젝트용 솔루션을 만들고 엽니다. 
   선택한 Azure QuickStart 템플릿은 리소스 그룹 프로젝트 내에서 azuredeploy.json이라는 배포 템플릿을 만듭니다. 이 배포 템플릿에는 HTTP 요청에 의해 트리거되고 Azure 함수를 호출하고 결과를 HTTP 응답으로 반환하는 간단한 논리 앱에 대한 정의가 포함되어 있습니다.

   ![새 서버리스 솔루션](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 다음으로 Azure에 솔루션을 배포합니다. 배포 템플릿을 열고 서버리스 앱의 리소스를 검토하려면 먼저 이 작업을 수행해야 합니다.

## <a name="deploy-your-solution"></a>솔루션 배포

Visual Studio의 논리 앱 디자이너에서 논리 앱을 열려면 Azure에 이미 배포된 Azure 리소스 그룹이 있어야 합니다. 그러면 디자이너에서 리소스 및 서비스에 대한 연결을 논리 앱에 만들 수 있습니다. 이 작업의 경우 다음 단계를 수행하여 Visual Studio에서 Azure Portal에 솔루션을 배포합니다.

1. 솔루션 탐색기에서 리소스 프로젝트의 바로 가기 메뉴에서**새** **시작 을** > 선택합니다.

   ![리소스 그룹에 대한 새 배포 만들기](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 아직 선택되지 않은 경우 Azure 구독 및 배포할 리소스 그룹을 선택합니다. 그런 다음 **배포를**선택합니다.

   ![배포 설정](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 매개 **변수 편집** 상자가 나타나면 배포 시 논리 앱 및 Azure 함수 앱에 사용할 리소스 이름을 제공한 다음 설정을 저장합니다. 함수 앱에 대해 전역적으로 고유한 이름을 사용해야 합니다.

   ![논리 앱 및 함수 앱에 대한 이름 제공](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio에서 지정한 리소스 그룹에 대한 배포를 시작하면 솔루션의 배포 상태가 Visual Studio **출력** 창에 표시됩니다. 
   배포가 완료되면 Azure Portal에서 해당 논리 앱이 활성 상태로 있습니다.

## <a name="edit-your-logic-app-in-visual-studio"></a>비주얼 스튜디오에서 논리 앱 편집

배포 후 논리 앱을 편집하려면 Visual Studio의 논리 앱 디자이너를 사용하여 논리 앱을 엽니다.

1. 솔루션 탐색기에서 azuredeploy.json 파일의 바로 가기 메뉴에서 **논리 앱 디자이너를 선택합니다.**

   ![논리 앱 디자이너에서 azuredeploy.json 열기](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

1. 논리 **앱 속성** 상자가 나타나면 **구독에서**아직 선택되지 않은 경우 Azure 구독을 선택합니다. **리소스 그룹에서**솔루션을 배포한 리소스 그룹 및 위치를 선택한 다음 **확인을**선택합니다.

   ![논리 앱 속성](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Logic Apps 디자이너가 열리면 단계를 계속 추가하거나 워크플로를 변경하고 업데이트를 저장할 수 있습니다.

   ![Logic Apps 디자이너에서 열린 논리 앱](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Azure Functions 프로젝트 만들기

자바 스크립트, 파이썬, F #, PowerShell, 배치 또는 Bash를 사용하여 함수 프로젝트 및 함수를 만들려면 [Azure Functions 핵심 도구 작업의](../azure-functions/functions-run-local.md)단계를 따르십시오. 솔루션 내에서 C#을 사용하여 Azure 함수를 개발하려면 [.NET 클래스](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)라이브러리 게시의 단계를 함수 앱으로 사용하여 C# 클래스 라이브러리를 사용합니다.

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio에서 함수 배포

배포 템플릿은 azuredeploy.json 파일의 변수에 의해 지정된 Git 리포지토리에서 솔루션에 있는 모든 Azure 함수를 배포합니다. 솔루션에서 Functions 프로젝트를 만들고 작성하는 경우 해당 프로젝트를 Git 소스 제어(예: GitHub 또는 Azure DevOps)에 확인한 다음 템플릿이 Azure 함수를 배포할 수 있도록 `repo` 변수를 업데이트할 수 있습니다.

## <a name="manage-logic-apps-and-view-run-history"></a>논리 앱 관리 및 실행 기록 보기

Azure에 이미 배포된 논리 앱의 경우 해당 앱을 Visual Studio에서 편집, 관리, 실행 기록 보기 및 비활성화할 수 있습니다.

1. Visual Studio의 **보기** 메뉴에서 **클라우드 탐색기**를 엽니다.

1. **모든 구독에서**관리하려는 논리 앱과 연결된 Azure 구독을 선택한 다음 **적용을**선택합니다.

1. **Logic Apps** 아래에서 논리 앱을 선택합니다. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

이제 이미 게시된 논리 앱을 리소스 그룹 프로젝트에 다운로드할 수 있습니다. 따라서 Azure Portal에서 논리 앱을 시작했을 수도 있지만 Visual Studio에서 해당 앱을 가져오고 관리할 수 있습니다. 자세한 내용은 [Visual Studio로 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 논리 앱 관리](manage-logic-apps-with-visual-studio.md)
