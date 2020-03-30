---
title: 배포를 위한 논리 앱 템플릿 만들기
description: Azure 논리 앱에서 배포를 자동화하기 위한 Azure 리소스 관리자 템플릿을 만드는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972641"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure 리소스 관리자 템플릿을 만들어 Azure 논리 앱에 대한 배포를 자동화합니다.

논리 앱 만들기 및 배포를 자동화하는 데 도움이 되도록 이 문서에서는 논리 앱에 대한 [Azure Resource Manager 템플릿을](../azure-resource-manager/management/overview.md) 만드는 방법에 대해 설명합니다. 워크플로 정의 및 배포에 필요한 기타 리소스가 포함된 템플릿의 구조 및 구문에 대한 개요는 [개요: Azure Resource Manager 템플릿을 사용한 논리 앱에 대한 배포 자동화를](logic-apps-azure-resource-manager-templates-overview.md)참조하십시오.

Azure Logic Apps는 논리 앱을 만들 뿐만 아니라 배포에 사용할 리소스 및 매개 변수를 정의하기 위해 재사용할 수 있는 미리 빌드된 논리 [앱 Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) 제공합니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> 템플릿의 연결이 논리 앱과 동일한 Azure 리소스 그룹 및 위치를 사용하는지 확인합니다.

Azure 리소스 관리자 템플릿에 대한 자세한 내용은 다음 항목을 참조하십시오.

* [Azure 리소스 관리자 템플릿 구조 및 구문](../azure-resource-manager/templates/template-syntax.md)
* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/template-syntax.md)
* [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>비주얼 스튜디오로 템플릿 만들기

배포할 준비가 된 유효한 매개 변수화된 논리 앱 템플릿을 만드는 가장 쉬운 방법은 Visual Studio(무료 커뮤니티 버전 이상) 및 Visual Studio용 Azure 논리 앱 도구를 사용합니다. 그런 다음 [Visual Studio에서 논리 앱을 만들거나](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [Azure 포털에서 기존 논리 앱을 찾아 다운로드하여 Visual Studio로 다운로드할](../logic-apps/manage-logic-apps-with-visual-studio.md)수 있습니다.

논리 앱을 다운로드하면 논리 앱에 대한 정의와 연결과 같은 기타 리소스가 포함된 템플릿을 얻을 수 있습니다. 또한 템플릿은 논리 앱 및 기타 리소스를 배포하는 데 사용되는 값에 대한 매개 변수를 *매개 변수화하거나*정의합니다. 별도의 매개 변수 파일에서 이러한 매개 변수에 대한 값을 제공할 수 있습니다. 이렇게 하면 배포 요구 사항에 따라 이러한 값을 보다 쉽게 변경할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visual Studio에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Azure PowerShell을 사용하여 템플릿 만들기

[LogicAppTemplate 모듈을](https://github.com/jeffhollan/LogicAppTemplateCreator)사용하여 Azure PowerShell을 사용하여 리소스 관리자 템플릿을 만들 수 있습니다. 이 오픈 소스 모듈은 먼저 논리 앱과 논리 앱에서 사용하는 모든 연결을 평가합니다. 그런 다음 모듈은 배포에 필요한 매개 변수를 가진 템플릿 리소스를 생성합니다.

예를 들어 Azure Service Bus 큐에서 메시지를 수신하고 Azure SQL 데이터베이스에 데이터를 업로드하는 논리 앱이 있다고 가정합니다. 이 모듈은 모든 오케스트레이션 논리를 보존하고 SQL 및 Service Bus 연결 문자열을 매개변수화하여 배포 요구 사항에 따라 해당 값을 제공하고 변경할 수 있도록 합니다.

이러한 샘플에서는 Azure 리소스 관리자 템플릿, Azure DevOps의 Azure 파이프라인 및 Azure PowerShell을 사용하여 논리 앱을 만들고 배포하는 방법을 보여 주며 다음과 같은 방법을 보여 준다.

* [샘플: Azure 논리 앱에서 Azure 서비스 버스 큐에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱에서 Azure 저장소 계정에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱에 대한 함수 앱 작업 설정](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure 논리 앱의 통합 계정에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell 모듈 설치

1. 아직 설치하지 않은 경우 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 설치합니다.

1. [PowerShell 갤러리에서](https://www.powershellgallery.com/packages/LogicAppTemplate)LogicAppTemplate 모듈을 설치하는 가장 쉬운 방법은 다음 명령을 실행하십시오.

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   최신 버전으로 업데이트하려면 다음 명령을 실행합니다.

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

또는 수동으로 설치하려면 [논리 앱 템플릿 작성자에](https://github.com/jeffhollan/LogicAppTemplateCreator)대한 GitHub의 단계를 따릅니다.

### <a name="install-azure-resource-manager-client"></a>Azure 리소스 관리자 클라이언트 설치

LogicAppTemplate 모듈이 모든 Azure 테넌트 및 구독 액세스 토큰과 함께 작동하려면 Azure 리소스 관리자 API를 호출하는 간단한 명령줄 [도구인 Azure 리소스 관리자 클라이언트 도구를](https://github.com/projectkudu/ARMClient)설치합니다.

이 도구를 `Get-LogicAppTemplate` 사용하여 명령을 실행하면 명령은 먼저 ARMClient 도구를 통해 액세스 토큰을 얻고 토큰을 PowerShell 스크립트에 파이프하고 템플릿을 JSON 파일로 만듭니다. 도구에 대한 자세한 내용은 [Azure 리소스 관리자 클라이언트 도구에 대한](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)이 문서를 참조하세요.

### <a name="generate-template-with-powershell"></a>PowerShell을 통해 템플릿 생성

LogicAppTemplate 모듈 및 [Azure CLI를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)설치한 후 템플릿을 생성하려면 이 PowerShell 명령을 실행합니다.

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

[Azure Resource Manager 클라이언트 도구에서](https://github.com/projectkudu/ARMClient)토큰의 파이핑에 대한 권장 사항을 `$SubscriptionId` 따르려면 Azure 구독 ID가 있는 대신 이 명령을 실행합니다.

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

추출 후 다음 명령을 실행하여 템플릿에서 매개 변수 파일을 만들 수 있습니다.

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure 키 볼트 참조(정적 전용)로 추출하려면 다음 명령을 실행합니다.

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 매개 변수 | 필수 | 설명 |
|------------|----------|-------------|
| TemplateFile | yes | 템플릿 파일에 대한 파일 경로 |
| KeyVault | 예 | 가능한 키 볼트 값을 처리하는 방법을 설명하는 열거형입니다. 기본값은 `None`입니다. |
||||

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
