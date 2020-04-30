---
title: 논리 앱 템플릿 배포
description: Azure Logic Apps에 대해 만든 Azure Resource Manager 템플릿을 배포 하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086439"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 Azure Resource Manager 템플릿 배포

논리 앱에 대 한 Azure Resource Manager 템플릿을 만든 후에 다음과 같은 방법으로 템플릿을 배포할 수 있습니다.

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure 리소스 관리자 REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure Portal를 통해 배포

논리 앱 템플릿을 Azure에 자동으로 배포 하려면 다음 **azure에 배포** 단추를 선택 하 여 Azure Portal에 로그인 하 고 논리 앱에 대 한 정보를 묻는 메시지를 표시 합니다. 그런 다음 논리 앱 템플릿이나 매개 변수를 필요한 대로 변경할 수 있습니다.

[![Azure에 배포](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

예를 들어 Azure Portal에 로그인 한 후 다음 정보를 입력 하 라는 메시지가 표시 됩니다.

* Azure 구독 이름
* 사용하려는 리소스 그룹
* 논리 앱 위치
* 논리 앱의 이름
* 테스트 URI
* 지정된 사용 약관 동의 여부

자세한 내용은 다음 항목을 참조하세요.

* [개요: Azure Resource Manager 템플릿을 사용 하 여 논리 앱에 대 한 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager 템플릿 및 Azure Portal를 사용 하 여 리소스 배포](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio를 사용하여 배포

Visual Studio를 사용 하 여 만든 Azure 리소스 그룹 프로젝트에서 논리 앱 템플릿을 배포 하려면 다음 단계에 따라 Azure에 [논리 앱을 수동으로 배포](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 합니다.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

특정 *Azure 리소스 그룹*에 배포 하려면 다음 명령을 사용 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

특정 *Azure 리소스 그룹*에 배포 하려면 다음 명령을 사용 합니다.

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [리소스 관리자 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps를 사용 하 여 배포

논리 앱 템플릿을 배포 하 고 환경을 관리 하기 위해 일반적으로 팀은 [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)의 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 와 같은 도구를 사용 합니다. Azure Pipelines는 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포 작업](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) 을 제공 합니다. 릴리스 파이프라인을 배포 하 고 생성 하기 위한 권한 부여를 위해 AD (Azure Active Directory) [서비스 사용자](../active-directory/develop/app-objects-and-service-principals.md)도 필요 합니다. [Azure Pipelines에서 서비스 사용자를 사용 하는](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)방법에 대해 자세히 알아보세요.

Azure Pipelines를 사용 하는 Azure Resource Manager 템플릿의 CI/CD에 대 한 지속적인 통합 및 지속적인 배포에 대 한 자세한 내용은 다음 항목 및 샘플을 참조 하세요.

* [Azure Pipelines와 리소스 관리자 템플릿 통합](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [자습서: Azure Pipelines를 사용한 Azure Resource Manager 템플릿의 지속적인 통합](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [샘플: Azure Logic Apps에서 Azure Service Bus 큐에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 Azure Storage 계정에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure DevOps에서 Azure Logic Apps에 대 한 함수 앱 작업 설정 및 Azure Pipelines를 사용 하 여 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 통합 계정에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [샘플: Azure Logic Apps를 사용 하 여 Azure Pipelines 오케스트레이션](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Azure Pipelines 사용에 대 한 일반적인 개략적인 단계는 다음과 같습니다.

1. Azure Pipelines에서 빈 파이프라인을 만듭니다.

1. 수동으로 생성 하거나 빌드 프로세스의 일부로 생성 하는 논리 앱 템플릿 및 템플릿 매개 변수 파일과 같이 파이프라인에 필요한 리소스를 선택 합니다.

1. 에이전트 작업에 대해 **Azure 리소스 그룹 배포** 작업을 찾아 추가 합니다.

   !["Azure 리소스 그룹 배포" 작업 추가](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [서비스 주체로](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)구성 합니다.

1. 논리 앱 템플릿 및 템플릿 매개 변수 파일에 대 한 참조를 추가 합니다.

1. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후에 논리 앱은 유효한 매개 변수를 사용 하 여 종단 간에 작동 합니다. 그러나 [자격 증명을 인증](../active-directory/develop/authentication-scenarios.md)하는 데 유효한 액세스 토큰을 생성 하려면 여전히 OAuth 연결에 권한을 부여 해야 합니다. OAuth 연결에 권한을 부여할 수 있는 방법은 다음과 같습니다.

* 자동 배포의 경우 각 OAuth 연결에 대 한 동의를 제공 하는 스크립트를 사용할 수 있습니다. [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 프로젝트의 GitHub에 있는 예제 스크립트는 다음과 같습니다.

* OAuth 연결에 대 한 권한을 수동으로 부여 하려면 Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 디자이너에서 필요한 모든 연결에 권한을 부여 합니다.

연결 권한을 부여 하는 대신 Azure Active Directory (Azure AD) [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md) 를 사용 하는 경우 [논리 앱 템플릿에서 서비스 주체 매개 변수를 지정](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 모니터링](../logic-apps/monitor-logic-apps.md)
