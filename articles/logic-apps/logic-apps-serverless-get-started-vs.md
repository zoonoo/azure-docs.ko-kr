---
title: Visual Studio에서 서버리스 앱 빌드 | Microsoft Docs
description: Visual Studio에서 Azure Logic Apps 및 Azure Functions을 사용하여 첫 번째 서버리스 앱을 빌드, 배포 및 관리합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.openlocfilehash: c172519984cce765217a713b276db5ccc8f67183
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558603"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Azure Logic Apps 및 Azure Functions를 사용하여 첫 번째 서버리스 앱 개발 - Visual Studio

Azure에서 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Azure Functions](../azure-functions/functions-overview.md)와 같은 서버리스 도구와 기능을 사용하여 클라우드 앱을 빠르게 개발하고 배포할 수 있습니다. 이 문서에서는 Visual Studio에서 Azure 함수를 호출하는 논리 앱을 사용하는 서버리스 앱의 빌드를 시작하는 방법을 보여 줍니다. Azure의 서버리스 솔루션에 대한 자세한 내용은 [Functions 및 Logic Apps가 있는 Azure Serverless](../logic-apps/logic-apps-serverless-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Visual Studio에서 서버리스 앱을 빌드하는 데 필요한 항목은 다음과 같습니다.

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [Visual Studio 2017](https://www.visualstudio.com/vs/) 또는 Visual Studio 2015 - Community, Professional 또는 Enterprise

* [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)(2.9.1 이상)

* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

* [Visual Studio 2017용 Azure Logic Apps 도구](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) 또는 [Visual Studio 2015 버전](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)

  Visual Studio Marketplace에서 직접 Azure Logic Apps 도구를 다운로드하여 설치하거나 [Visual Studio 내에서 이 확장을 설치하는 방법](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)을 알아볼 수 있습니다. 설치가 완료되면 Visual Studio를 다시 시작하세요.

* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)(로컬로 Functions 디버깅용)

* Visual Studio에 포함된 Logic Apps 디자이너를 사용하는 동안 웹에 액세스

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다. 예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

## <a name="create-resource-group-project"></a>리소스 그룹 프로젝트 만들기

시작하려면 서버리스 앱에 대한 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 만듭니다. Azure에서 전체 앱에 대한 리소스를 단일 자산으로 구성, 관리 및 배포하는 데 사용하는 논리적 컬렉션인 리소스 그룹 내에 리소스를 만듭니다. Azure에 있는 서버리스 앱의 경우 리소스 그룹에는 Azure Logic Apps 및 Azure Functions용 리소스가 포함되어 있습니다. [Azure 리소스 그룹 및 리소스](../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.

1. Visual Studio를 시작하고 Azure 계정으로 로그인합니다.

1. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

   ![Visual Studio에서 새 프로젝트 만들기](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **설치됨** 아래에서 **Visual C#** 또는 **Visual Basic**을 선택합니다. **클라우드** > **Azure 리소스 그룹**을 선택합니다.

   **클라우드** 범주 또는 **Azure 리소스 그룹** 프로젝트가 없으면 Visual Studio용 Azure SDK를 설치했는지 확인합니다.

1. 프로젝트 이름과 위치를 제공한 다음, **확인**을 선택합니다.

   Visual Studio에서 템플릿을 선택하라는 메시지가 표시됩니다. 빈 템플릿, Logic App 템플릿 또는 다른 템플릿을 사용하여 시작할 수 있지만, 이 예에서는 논리 앱 및 Azure 함수에 대한 호출이 포함된 서버리스 앱을 빌드하는 Azure 빠른 시작 템플릿을 사용합니다.

   Visual Studio에서 논리 앱만 만들려면 **Logic App** 템플릿을 선택합니다. 이 템플릿은 솔루션을 Azure 리소스 그룹에 미리 배포할 필요 없이 Logic Apps 디자이너에서 열리는 빈 논리 앱을 만듭니다.

1. **이 위치의 템플릿 표시** 아래에서 **Azure 빠른 시작(GitHub/Azure/azure-quickstart-templates)** 을 선택합니다.

1. 검색 상자에서 필터로 “logic-app”을 입력하고, 다음 서버리스 빠른 시작 템플릿을 선택한 이후 **확인**을 선택합니다. **101-logic-app-and-function-app**

   ![Azure 빠른 시작 템플릿 선택](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio에서 리소스 그룹 프로젝트용 솔루션을 만들고 엽니다. 선택한 빠른 시작 템플릿은 리소스 그룹 프로젝트 내에 `azuredeploy.json`이라는 배포 템플릿을 만듭니다. 이 배포 템플릿에는 HTTP 요청에서 트리거하고, Azure 함수를 호출한 다음, 결과를 HTTP 응답으로 반환하는 간단한 논리 앱에 대한 정의가 포함되어 있습니다.
   
   ![새 서버리스 솔루션](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 다음으로, 솔루션을 Azure에 배포한 후에 배포 템플릿을 열고 서버리스 앱에 대한 리소스를 검토해야 합니다.

## <a name="deploy-your-solution"></a>솔루션 배포

Visual Studio에서 Logic Apps 디자이너를 사용하여 논리 앱을 열기 전에 Azure에 이미 배포된 Azure 리소스 그룹이 있어야 합니다. 그러면 디자이너에서 리소스 및 서비스에 대한 연결을 논리 앱에 만들 수 있습니다. 이 작업을 위해 솔루션을 Visual Studio에서 Azure Portal로 배포합니다.

1. [솔루션 탐색기]에서 리소스 프로젝트의 바로 가기 메뉴를 연 다음, **배포** > **새로 만들기**를 차례로 선택합니다.

   ![리소스 그룹에 대한 새 배포 만들기](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 아직 선택하지 않았으면 배포하려는 Azure 구독 및 리소스 그룹을 선택합니다. **배포**를 선택합니다.

   ![배포 설정](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. **매개 변수 편집** 상자가 표시되면 배포할 때 사용할 논리 앱과 Azure 함수 앱에 대한 리소스 이름을 입력한 다음, 해당 설정을 저장합니다. 함수 앱에 대해 전역적으로 고유한 이름을 사용해야 합니다.

   ![논리 앱 및 함수 앱에 대한 이름 제공](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio에서 지정한 리소스 그룹에 대한 배포를 시작하면 솔루션의 배포 상태가 Visual Studio **출력** 창에 표시됩니다. 배포가 완료되면 Azure Portal에서 해당 논리 앱이 활성 상태로 있습니다.

## <a name="edit-logic-app-in-visual-studio"></a>Visual Studio에서 논리 앱 편집

이제 솔루션이 리소스 그룹에 배포되었으므로 Logic Apps 디자이너에서 논리 앱을 열어 편집하고 변경할 수 있습니다.

1. [솔루션 탐색기]에서 `azuredeploy.json` 파일의 바로 가기 메뉴를 연 다음, **Logic Apps 디자이너로 열기**를 선택합니다.

   ![Logic Apps 디자이너에서 "azuredeploy.json" 열기](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. **논리 앱 속성** 상자가 나타나고 아직 선택하지 않은 경우 **구독** 아래에서 Azure 구독을 선택합니다. **리소스 그룹** 아래에서 솔루션을 배포한 리소스 그룹과 위치를 선택한 다음, **확인**을 선택합니다.

   ![논리 앱 속성](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Logic Apps 디자이너가 열리면 단계를 계속 추가하거나 워크플로를 변경하고 업데이트를 저장할 수 있습니다.

   ![Logic Apps 디자이너에서 열린 논리 앱](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Azure Functions 프로젝트 만들기

JavaScript, Python, F#, PowerShell, Batch 또는 Bash를 사용하여 Functions 프로젝트 및 함수를 만들려면 [Azure Functions Core Tools](../azure-functions/functions-run-local.md) 문서의 단계를 따릅니다. 솔루션 내부에서 C#으로 Azure 함수를 개발하려면 [ .NET 클래스 라이브러리를 Function App으로 게시](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) 문서의 단계에 따라 C# 클래스 라이브러리를 사용할 수 있습니다.

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio에서 함수 배포

배포 템플릿은 솔루션에 있는 Azure 함수를 `azuredeploy.json` 파일의 변수로 지정된 Git 리포지토리에서 배포합니다. 솔루션에서 Functions 프로젝트를 만들고 작성하는 경우 해당 프로젝트를 Git 소스 제어(예: GitHub 또는 Azure DevOps)에서 확인한 다음, `repo` 변수를 업데이트하여 템플릿에서 Azure 함수를 배포할 수 있습니다.

## <a name="manage-logic-apps-and-view-run-history"></a>논리 앱 관리 및 실행 기록 보기

Azure에 이미 배포된 논리 앱에 대해서도 Visual Studio에서 해당 앱에 대한 편집, 관리, 실행 기록 보기 및 사용 해제를 수행할 수 있습니다.

1. Visual Studio의 **보기** 메뉴에서 **클라우드 탐색기**를 엽니다.

1. **모든 구독** 아래에서 관리하려는 논리 앱과 연결된 Azure 구독을 선택하고, **적용**을 선택합니다.

1. **Logic Apps** 아래에서 논리 앱을 선택합니다. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

이제 이미 게시된 논리 앱을 리소스 그룹 프로젝트에 다운로드할 수 있습니다. 따라서 Azure Portal에서 논리 앱을 시작한 경우에도 Visual Studio에서 해당 앱을 가져와서 관리할 수 있습니다. 자세한 내용은 [Visual Studio로 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [서버리스 소셜 대시보드 작성](logic-apps-scenario-social-serverless.md)
* [Visual Studio에서 논리 앱 관리](manage-logic-apps-with-visual-studio.md)
* [논리 앱 워크플로 정의 언어](logic-apps-workflow-definition-language.md)
