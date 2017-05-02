---
title: "PowerShell 및 템플릿으로 리소스 배포 | Microsoft Docs"
description: "Azure Resource Manager와 Azure PowerShell을 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>리소스 관리자 템플릿과 Azure PowerShell로 리소스 배포

이 항목은 리소스 관리자 템플릿으로 Azure PowerShell을 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. 템플릿은 로컬 파일이거나 URI를 통해 사용 가능한 외부 파일일 수 있습니다.

[첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md#final-template) 문서에서 이러한 예에서 사용된 템플릿(stroage.json)을 가져올 수 있습니다. 이러한 예의 템플릿을 사용하려면 JSON 파일을 만들고 복사된 콘텐츠를 복사합니다.

## <a name="deploy-local-template"></a>로컬 템플릿 배포
배포를 신속하게 시작하려면 다음 cmdlet과 인라인 매개 변수를 함께 사용하여 로컬 템플릿을 배포합니다. 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 메시지가 표시됩니다.

```powershell
ProvisioningState       : Succeeded
```

이전 예에서는 기본 구독에 리소스 그룹을 만들었습니다. 다른 구독을 사용하려면 로그인한 다음 [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) cmdlet을 추가합니다.

## <a name="deploy-external-template"></a>외부 템플릿 배포

외부 템플릿을 배포하려면 **TemplateUri** 매개 변수를 사용합니다. 템플릿은 공개적으로 액세스 가능한 모든 URI(예: 저장소 계정의 파일)에 있을 수 있습니다.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

액세스할 공유 액세스 서명(SAS) 토큰을 요구하여 템플릿을 보호할 수 있습니다. SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조하세요.

## <a name="parameter-files"></a>매개 변수 파일

앞의 예에서는 매개 변수를 인라인 값으로 전달하는 방법을 보여줍니다. 파일에서 매개 변수 값을 지정하고 배포 중에 해당 파일을 전달할 수 있습니다. 

매개 변수 파일은 다음과 같은 형식이어야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

로컬 매개 변수 파일을 전달하려면 **TemplateParameterFile** 매개 변수를 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

외부 매개 변수 파일을 전달하려면 **TemplateParameterUri** 매개 변수를 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

하지만 외부 매개 변수 파일을 사용하면 인라인 또는 로컬 파일에서 다른 값을 전달할 수 없습니다. **TemplateParameterUri** 매개 변수에서 매개 변수 파일을 지정하는 경우 모든 인라인 매개 변수는 무시됩니다. 외부 파일에서 모든 매개 변수 값을 제공해야 합니다. 템플릿이 매개 변수 파일에 포함할 수 없는 중요한 값을 포함하는 경우 해당 값을 키 자격 증명 모음에 추가하고 동적으로 모든 매개 변수 값을 인라인으로 제공합니다.

템플릿에 PowerShell 명령의 매개 변수 중 하나와 이름이 같은 매개 변수가 포함되어 있으면 PowerShell에서 접미사가 **FromTemplate**인 템플릿에서 매개 변수를 제공합니다. 예를 들어 템플릿의 **ResourceGroupName**이라는 매개 변수는 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. **ResourceGroupNameFromTemplate**에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

## <a name="test-a-deployment"></a>배포 테스트

리소스를 실제로 배포하지 않고 템플릿과 매개 변수 값을 테스트하려면 [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment)를 사용합니다. 로컬 또는 원격 파일을 사용하기 위한 옵션이 모두 동일합니다.

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>디버깅할 배포 콘텐츠 로깅

배포 작업에 대한 정보는 활동 로그에 자동으로 로깅됩니다. 그러나 배포 오류 문제를 해결하는 데 도움이 될 수 있는 배포에 대한 추가 정보를 기록하려면 **DeploymentDebugLogLevel** 매개 변수를 사용합니다. 요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다가 배포 작업과 함께 기록되도록 지정할 수 있습니다.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

로그를 보는 데 대한 정보는 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](resource-group-audit.md)를 참조하세요.

## <a name="export-resource-manager-template"></a>Resource Manager 템플릿 내보내기
기존 리소스 그룹(PowerShell 또는 포털을 비롯한 다른 방법 중 하나를 통해 배포된)의 경우 리소스 그룹에 대 한 Resource Manager 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 모든 인프라가 템플릿에 정의되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.
2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

리소스 그룹에 대한 템플릿을 보려면 [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) Cmdlet을 실행합니다.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

자세한 내용은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

완료 모드를 사용하려면 `Mode` 매개 변수를 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>다음 단계
* 템플릿을 배포하는 전체 샘플 스크립트는 [Resource Manager 템플릿 배포 스크립트](resource-manager-samples-powershell-deploy.md)를 참조하세요.
* 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


