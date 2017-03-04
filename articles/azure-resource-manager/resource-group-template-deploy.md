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
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a
ms.lasthandoff: 01/18/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>리소스 관리자 템플릿과 Azure PowerShell로 리소스 배포
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [포털](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

이 항목은 리소스 관리자 템플릿으로 Azure PowerShell을 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. 템플릿은 로컬 파일이거나 URI를 통해 사용 가능한 외부 파일일 수 있습니다. 템플릿이 저장소 계정에 상주하는 경우, 템플릿에 대한 액세스를 제한하고 배포 중에 공유 액세스 서명(SAS) 토큰을 제공할 수 있습니다.

> [!TIP]
> 배포 중 발생하는 오류 디버깅에 대한 도움을 받으려면 다음을 참조하세요.
> 
> * [배포 작업 보기](resource-manager-deployment-operations.md)에서 오류를 해결하는 데 유용한 정보를 알 수 있습니다.
> * [Azure Resource Manager로 Azure에 리소스를 배포할 때 발생하는 일반적인 오류 해결](resource-manager-common-deployment-errors.md) 에서 일반적인 배포 오류를 해결하는 방법을 알 수 있습니다.
> 
> 

## <a name="quick-steps-to-deployment"></a>배포에 대한 빠른 단계
신속하게 배포를 시작하려면 다음 명령을 사용합니다.

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

이러한 명령은 리소스 그룹을 만들고 해당 리소스 그룹에 템플릿을 배포합니다. 템플릿 파일 및 매개 변수 파일은 모두 로컬 파일입니다. 작동하는 경우 리소스를 배포하는 데 필요한 모든 항목이 있는 것입니다. 하지만 배포할 리소스를 지정하는 데 더 많은 옵션을 사용할 수 있습니다. 이 문서의 나머지 부분에서는 배포하는 동안 사용할 수 있는 옵션을 모두 설명합니다. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>배포
1. Azure 계정에 로그인

   ```powershell
   Login-AzureRmAccount
   ```

    사용자 계정에 대한 요약이 반환됩니다.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. 여러 구독이 있는 경우 **Set-AzureRmContext** 명령을 사용하여 배포에 사용할 구독 ID를 제공합니다. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. 템플릿을 배포할 때는 배포된 리소스가 들어 있는 리소스 그룹을 지정해야 합니다. 배포할 기존 리소스 그룹이 있다면 이 단계를 건너뛰고 해당 리소스 그룹을 사용하면 됩니다. 
   
     리소스 그룹을 만들려면 리소스 그룹에 이름과 위치를 지정합니다. 그룹 리소스가 리소스에 대한 메타데이터를 저장하기 때문에 리소스 그룹에 대한 위치를 제공합니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     새 리소스 그룹에 대한 요약이 반환됩니다.
4. 배포를 실행하기 전에 배포 설정의 유효성을 검사할 수 있습니다. **Test-AzureRmResourceGroupDeployment** Cmdlet을 사용하면 실제 리소스를 만들기 전에 문제를 찾아낼 수 있습니다. 다음 예제는 배포의 유효성 검사 방법을 보여줍니다.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. 리소스 그룹에 리소스를 배포하려면 **New-AzureRmResourceGroupDeployment** 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름, 템플릿의 경로 또는 URL 및 시나리오에 필요한 기타 매개 변수가 포함됩니다. **Mode** 매개 변수가 지정되지 않은 경우 기본값 **Incremental**이 사용됩니다. 전체 배포를 실행하려면 **Mode**를 **Complete**로 설정합니다. 이 완전한 모드를 사용할 때는 템플릿에 없는 리소스를 실수로 삭제할 수 있으므로 주의해야 합니다.
   
     로컬 템플릿을 배포하려면 **TemplateFile** 매개 변수를 사용합니다.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     외부 템플릿을 배포하려면 **TemplateUri** 매개 변수를 사용합니다.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     앞의 두 예제에는 매개 변수 값이 포함되지 않았습니다. [매개 변수](#parameters) 섹션에서 매개 변수 값을 전달하는 옵션에 대해 알아봅니다. 이제 다음 구문으로 매개 변수 값을 제공하라는 메시지가 표시됩니다.

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     리소스가 배포된 후에 배포 요약이 나타납니다. 요약에는 **ProvisioningState**가 포함되며 배포의 성공 여부를 나타냅니다.

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. 배포 오류 문제를 해결하는 데 도움이 될 수 있는 배포에 대한 추가 정보를 기록하려면 **DeploymentDebugLogLevel** 매개 변수를 사용합니다. 요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다가 배포 작업과 함께 기록되도록 지정할 수 있습니다.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     이 디버깅 콘텐츠를 사용하여 배포 문제를 해결하는 방법에 대한 자세한 내용은 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.

## <a name="deploy-private-template-with-sas-token"></a>SAS 토큰으로 개인 템플릿 배포
SAS 토큰으로 배포 중에 저장소 계정에 템플릿을 추가하고 이를 연결할 수 있습니다.

> [!IMPORTANT]
> 아래 단계를 따르면 템플릿을 포함한 blob은 계정 소유자만 액세스할 수 있습니다. 그러나 blob용 SAS 토큰을 생성하면 해당 blob은 해당 URI를 가진 사람이면 누구나 액세스할 수 있습니다. 다른 사용자가 URI를 가로채는 경우, 그 사용자가 템플릿에 액세스할 수 있습니다. SAS 토큰을 사용하는 것은 템플릿에 액세스를 제한하는 좋은 방법이지만 템플릿에 암호와 같은 민감한 데이터를 직접 입력하지 말아야 합니다.
> 
> 

### <a name="add-private-template-to-storage-account"></a>저장소 계정에 개인 템플릿 추가
다음 단계에서는 템플릿용 저장소 계정을 설정합니다.

1. 리소스 그룹을 만듭니다.
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. 저장소 계정을 만듭니다. 저장소 계정 이름은 Azure에&1;개뿐이어야 하므로 해당 계정에 고유한 이름을 입력합니다.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. 현재 저장소 계정을 설정합니다.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. 컨테이너를 만듭니다. 권한은 **Off**로 설정합니다. 즉, 이 컨테이너는 소유자만 액세스할 수 있습니다.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. 컨테이너에 템플릿을 주가합니다.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>배포하는 동안 SAS 토큰 제공
저장소 계정에 개인 템플릿을 배포하려면 SAS 토큰을 검색하고 해당 템플릿의 URI에 포함합니다.

1. 현재 저장소 계정을 변경했다면 현재 저장소 계정을 템플릿을 포함하는 것으로 설정합니다.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. 읽기 권한과 만료 기간이 있는 SAS 토큰을 만들어서 액세스를 제한합니다. SAS 토큰을 포함한 템플릿의 전체 URI를 검색합니다.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. SAS 토큰을 포함한 URI를 제공하여 템플릿을 배포합니다.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

연결된 템플릿에 SAS 토큰을 사용하는 예제는 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

## <a name="parameters"></a>매개 변수

다음과 같은 방법으로 매개 변수 값을 제공할 수 있습니다. 
   
- 인라인 매개 변수입니다. cmdlet에 개별 매개 변수 이름을 포함합니다(예: **-myParameterName**).

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- 매개 변수 개체입니다. **-TemplateParameterObject** 매개 변수를 포함합니다.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- 로컬 매개 변수 파일입니다. **-TemplateParameterFile** 매개 변수를 포함합니다.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- 외부 매개 변수 파일입니다. **-TemplateParameterUri** 매개 변수를 포함합니다.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

템플릿에 PowerShell 명령의 매개 변수 중 하나와 이름이 같은 매개 변수가 포함되어 있으면 해당 매개 변수의 값을 입력하라는 메시지가 표시됩니다. Azure PowerShell은 접미사 **FromTemplate**으로 템플릿의 매개 변수를 표시합니다. 예를 들어 템플릿의 **ResourceGroupName**이라는 매개 변수는 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. **ResourceGroupNameFromTemplate**에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

## <a name="parameter-precedence"></a>매개 변수 우선 순위

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

하지만 외부 매개 변수 파일을 사용하면 인라인 또는 로컬 파일에서 다른 값을 전달할 수 없습니다. **TemplateParameterUri** 매개 변수에서 매개 변수 파일을 지정하는 경우 모든 인라인 매개 변수는 무시됩니다. 외부 파일에서 모든 매개 변수 값을 제공해야 합니다. 템플릿이 매개 변수 파일에 포함할 수 없는 중요한 값을 포함하는 경우 해당 값을 키 자격 증명 모음에 추가하고 동적으로 모든 매개 변수 값을 인라인으로 제공합니다.

## <a name="next-steps"></a>다음 단계
* .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
* 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
* 배포 자동화에 대한&4;가지 시리즈는 [Azure 가상 컴퓨터에 대한 응용 프로그램 배포 자동화](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 이 시리즈에서는 응용 프로그램 아키텍처, 액세스 및 보안, 가용성 및 규모, 응용 프로그램 배포에 대해 다룹니다.


