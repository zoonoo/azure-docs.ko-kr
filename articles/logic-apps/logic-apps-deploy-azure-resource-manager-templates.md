---
title: 논리 앱 템플릿 배포
description: Azure 논리 앱에 대해 만든 Azure 리소스 관리자 템플릿을 배포하는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270447"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure 논리 앱에 대한 Azure 리소스 관리자 템플릿 배포

논리 앱에 대한 Azure 리소스 관리자 템플릿을 만든 후 다음과 같은 방법으로 템플릿을 배포할 수 있습니다.

* [Azure 포털](#portal)
* [Visual Studio](#visual-studio)
* [Azure 파워쉘](#powershell)
* [Azure CLI](#cli)
* [Azure 리소스 관리자 REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure 데브옵스](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure 포털을 통한 배포

Azure에 논리 앱 템플릿을 자동으로 배포하려면 Azure 포털에 로그인하고 논리 앱에 대한 정보를 묻는 다음 **Azure 배포** 단추를 선택할 수 있습니다. 그런 다음 논리 앱 템플릿 또는 매개 변수를 필요한 대로 변경할 수 있습니다.

[![Azure에 배포](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

예를 들어 Azure 포털에 로그인한 후 다음 정보가 표시됩니다.

* Azure 구독 이름
* 사용하려는 리소스 그룹
* 논리 앱 위치
* 논리 앱의 이름
* 테스트 URI
* 지정된 사용 약관 동의 여부

자세한 내용은 다음 항목을 참조하세요.

* [개요: Azure 리소스 관리자 템플릿을 사용 하 여 논리 애플 리 케이 션에 대 한 배포 를 자동화](logic-apps-azure-resource-manager-templates-overview.md)
* [Azure 리소스 관리자 템플릿 및 Azure 포털을 사용 하 고 리소스 배포](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio를 사용하여 배포

Visual Studio를 사용하여 만든 Azure 리소스 그룹 프로젝트에서 논리 앱 템플릿을 배포하려면 다음 [단계를 수행하여 논리 앱을 Azure에 수동으로 배포합니다.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

특정 *Azure 리소스 그룹에*배포하려면 다음 명령을 사용합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

특정 *Azure 리소스 그룹에*배포하려면 다음 명령을 사용합니다.

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [리소스 관리자 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps로 배포

논리 앱 템플릿을 배포하고 환경을 관리하기 위해 팀은 일반적으로 [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)에서 [Azure 파이프라인과](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 같은 도구를 사용합니다. Azure 파이프라인은 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포 작업을](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) 제공합니다. 릴리스 파이프라인을 배포하고 생성하려면 AD(Azure Active Directory) [서비스 주체도](../active-directory/develop/app-objects-and-service-principals.md)필요합니다. [Azure 파이프라인을 사용하여 서비스 주체를 사용하는](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)방법에 대해 자세히 알아봅니다.

Azure 파이프라인을 사용하여 Azure 리소스 관리자 템플릿에 대한 지속적인 통합 및 지속적인 배포(CI/CD)에 대한 자세한 내용은 다음 항목 및 샘플을 참조하십시오.

* [리소스 관리자 템플릿을 Azure 파이프라인과 통합](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [자습서: Azure 파이프라인을 사용 하 고 Azure 리소스 관리자 템플릿에 대 한 지속적인 통합](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [샘플: Azure 논리 앱에서 Azure Service 버스 큐에 연결하고 Azure DevOps에서 Azure 파이프라인으로 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱에서 Azure 저장소 계정에 연결하고 Azure DevOps에서 Azure 파이프라인으로 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱에 대한 함수 앱 작업을 설정하고 Azure DevOps에서 Azure 파이프라인으로 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱의 통합 계정에 연결하고 Azure DevOps에서 Azure 파이프라인으로 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [샘플: Azure 논리 앱을 사용하여 Azure 파이프라인을 오케스트레이션합니다.](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Azure 파이프라인을 사용하기 위한 일반적인 상위 수준 단계는 다음과 같습니다.

1. Azure 파이프라인에서 빈 파이프라인을 만듭니다.

1. 수동으로 또는 빌드 프로세스의 일부로 생성하는 논리 앱 템플릿 및 템플릿 매개 변수 파일과 같이 파이프라인에 필요한 리소스를 선택합니다.

1. 에이전트 작업의 경우 **Azure 리소스 그룹 배포** 작업을 찾아 추가합니다.

   !["Azure 리소스 그룹 배포" 작업 추가](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [서비스 주체로 구성합니다.](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)

1. 논리 앱 템플릿 및 템플릿 매개 변수 파일에 참조를 추가합니다.

1. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후 논리 앱은 유효한 매개 변수를 사용하여 종단 간 작동합니다. 그러나 [자격 증명을 인증하기](../active-directory/develop/authentication-scenarios.md)위한 유효한 액세스 토큰을 생성하려면 OAuth 연결에 권한을 부여해야 합니다. OAuth 연결을 승인할 수 있는 방법은 다음과 같습니다.

* 자동화된 배포의 경우 각 OAuth 연결에 대한 동의를 제공하는 스크립트를 사용할 수 있습니다. 다음은 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 프로젝트의 GitHub의 예제 스크립트입니다.

* OAuth 연결을 수동으로 승인하려면 Azure 포털 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 디자이너에서 필요한 연결을 승인합니다.

Azure Active Directory(Azure AD) [서비스 주체를](../active-directory/develop/app-objects-and-service-principals.md) 사용하여 연결을 인증하는 경우 [논리 앱 템플릿에서 서비스 주체 매개 변수를 지정하는](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 모니터링](../logic-apps/monitor-logic-apps.md)
