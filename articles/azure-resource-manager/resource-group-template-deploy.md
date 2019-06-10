---
title: PowerShell 및 템플릿으로 리소스 배포 | Microsoft Docs
description: Azure Resource Manager 및 Azure PowerShell을 사용 하 여 Azure 리소스를 배포 합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 63d729f19b0ef20d0e7a716d6857b4627095856b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476987"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>리소스 관리자 템플릿과 Azure PowerShell로 리소스 배포

Resource Manager 템플릿으로 Azure PowerShell을 사용하여 Azure에 리소스를 배포하는 방법을 알아봅니다. Azure 솔루션 배포 및 관리 개념에 대한 자세한 내용은 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>배포 범위

Azure 구독 또는 구독 내에서 리소스 그룹 배포를 대상으로 지정할 수 있습니다. 대부분의 경우에서 리소스 그룹 배포를 대상으로 수 있습니다. 구독 배포를 사용 하 여 정책 및 역할 할당은 구독에서 적용 하 합니다. 또한 리소스 그룹을 만들고 리소스를 배포할 구독 배포를 사용 합니다. 배포의 범위에 따라 다른 명령을 사용할 수 있습니다.

배포 하는 **리소스 그룹**를 사용 하 여 [새로 만들기-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

배포 하는 **구독**를 사용 하 여 [새로 만들기-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

현재 REST API를 통해 관리 그룹 배포만 지원 됩니다. 참조 [Resource Manager 템플릿과 Resource Manager REST API를 사용 하 여 리소스 배포](resource-group-template-deploy-rest.md)합니다.

이 문서의 예제에서는 리소스 그룹 배포를 사용합니다. 구독 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](deploy-to-subscription.md)합니다.

## <a name="prerequisites"></a>필수 조건

템플릿을 배포 해야 합니다. 다운로드 하 고 저장 하면 이미 계정이 없는 경우는 [예제 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) Azure 빠른 시작 템플릿 리포지토리에서 합니다. 이 문서에 사용된 로컬 파일 이름은 **c:\MyTemplates\azuredeploy.json**입니다.

템플릿을 배포 하는 Azure Cloud shell을 사용 하지 않는 한 Azure PowerShell을 설치 하 고 Azure에 연결 해야 합니다.

- **로컬 컴퓨터에 Azure PowerShell cmdlet을 설치합니다.** 자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요.
- **[Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)를 사용하여 Azure에 연결합니다**. Azure 구독이 여러 개인 경우 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)를 실행해야 할 수도 있습니다. 자세한 내용은 [여러 Azure 구독 사용](/powershell/azure/manage-subscriptions-azureps)을 참조하세요.

## <a name="deploy-local-template"></a>로컬 템플릿 배포

다음 예제에서는 리소스 그룹을 만들고 로컬 컴퓨터에서 템플릿을 배포 합니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 마침표로 끝날 수 없습니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="deploy-remote-template"></a>원격 템플릿 배포

로컬 컴퓨터에 Resource Manager 템플릿을 저장하는 대신, 외부 위치에 저장할 수 있습니다. 원본 제어 리포지토리(예: GitHub)에 템플릿을 저장할 수 있습니다. 또는 조직에서 공유 액세스에 대한 Azure Storage 계정에 저장할 수 있습니다.

외부 템플릿을 배포하려면 **TemplateUri** 매개 변수를 사용합니다. 예제의 URI를 사용하여 GitHub에서 샘플 템플릿을 배포합니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

앞의 예제에서는 템플릿에 중요한 데이터가 포함되어 있지 않으므로 대부분의 시나리오에 적합한 이 템플릿에 대해 공개적으로 액세스할 수 있는 URI가 필요합니다. 중요한 데이터(예: 관리자 암호)를 지정해야 하는 경우 해당 값을 안전한 매개 변수로 전달합니다. 그러나 템플릿에 공개적으로 액세스할 수 있도록 하지 않으려면 프라이빗 스토리지 컨테이너에 저장하여 보호할 수 있습니다. SAS(공유 액세스 서명) 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조하세요. 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요.

## <a name="deploy-from-azure-cloud-shell"></a>Azure Cloud shell에서 배포

[Azure Cloud Shell](https://shell.azure.com)을 사용하여 템플릿을 배포할 수 있습니다. 외부 템플릿을 배포하려면 템플릿의 URI를 제공합니다. 로컬 템플릿을 배포하려면 먼저 Cloud Shell용 스토리지 계정에 템플릿을 로드해야 합니다. 셸에 파일을 업로드하려면 셸 창에서 **파일 업로드/다운로드** 메뉴 아이콘을 선택합니다.

Cloud Shell을 열려면 [https://shell.azure.com](https://shell.azure.com)으로 이동하거나, 다음 코드 섹션에서 **Try-It**을 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

셸에 코드를 붙여넣으려면 셸 내부를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능은 라고도 *오류 발생 시 롤백*합니다. 배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 재배포를 지정하려면 배포 명령에 `-RollbackToLastDeployment` 또는 `-RollBackDeploymentName` 매개 변수를 사용합니다. 이 기능은 인프라 배포에 대 한 알려진된 좋은 상태로 했습니다를이 상태로 되돌리려는 경우에 유용 합니다. 주의 및 제한 사항이 있습니다.

- 재배포는 동일한 매개 변수를 사용 하 여 이전에 실행 했던 대로 실행 됩니다. 매개 변수를 변경할 수 없습니다.
- 이전 배포를 사용 하 여 실행 되는 [전체 모드](./deployment-modes.md#complete-mode)합니다. 이전 배포에 포함 되지 모든 리소스가 삭제 되 고 모든 리소스 구성을 이전 상태로 설정 됩니다. 완벽 하 게 이해 해야 합니다 [배포 모드](./deployment-modes.md)합니다.
- 리소스에만 영향을 줍니다 재배포, 모든 데이터 변경 내용은 영향을 받지 않습니다.
- 이 기능은 리소스 그룹 배포의 경우 구독 수준 배포에만 지원 됩니다. 구독 수준 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](./deploy-to-subscription.md)합니다.

이 옵션을 사용하려면 배포가 배포 기록에서 식별될 수 있도록 고유한 이름을 지정해야 합니다. 고유한 이름이 없으면 현재 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다. 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

성공한 마지막 배포를 다시 배포하려면 `-RollbackToLastDeployment` 매개 변수를 플래그로 추가합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

특정 배포를 다시 배포하려면 `-RollBackDeploymentName` 매개 변수를 사용하고 배포의 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

지정된 배포는 분명히 성공했을 것입니다.

## <a name="pass-parameter-values"></a>매개 변수 값 전달

매개 변수 값을 전달하려면 인라인 매개 변수 또는 매개 변수 파일을 사용할 수 있습니다. 이 문서의 이전 예제에서는 인라인 매개 변수를 보여 줍니다.

### <a name="inline-parameters"></a>인라인 매개 변수

인라인 매개 변수를 전달하려면 `New-AzResourceGroupDeployment` 명령을 사용하여 매개 변수 이름을 제공합니다. 예를 들어, 문자열 및 배열을 템플릿에 전달하려면 다음을 사용합니다.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

파일의 콘텐츠를 가져와서 해당 콘텐츠를 인라인 매개 변수로 제공할 수도 있습니다.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

파일에서 매개 변수 값을 가져오면 구성 값을 제공해야 하는 경우에 유용합니다. 예를 들어, [Linux 가상 머신에 대한 Cloud-Init 값](../virtual-machines/linux/using-cloud-init.md)을 제공할 수 있습니다.

개체의 배열을 전달 해야 할 경우 PowerShell에서 해시 테이블을 만들기 및 배열에 추가 합니다. 배포 중 매개 변수로 해당 배열에 전달 합니다.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```


### <a name="parameter-files"></a>매개 변수 파일

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 매개 변수 파일은 로컬 파일이거나 액세스 가능한 URI가 있는 외부 파일일 수 있습니다.

매개 변수 파일은 다음과 같은 형식이어야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

매개 변수 섹션에는 템플릿에 정의된 매개 변수(storageAccountType)와 일치하는 매개 변수 이름이 포함되어 있습니다. 매개 변수 파일에는 매개 변수의 값이 포함됩니다. 이 값은 배포 동안 템플릿에 자동으로 전달됩니다. 둘 이상의 매개 변수 파일을 만든 후 시나리오에 적합한 매개 변수 파일을 전달할 수 있습니다.

앞의 예제를 복사하고 `storage.parameters.json`이라는 파일로 저장합니다.

로컬 매개 변수 파일을 전달하려면 **TemplateParameterFile** 매개 변수를 사용합니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

외부 매개 변수 파일을 전달하려면 **TemplateParameterUri** 매개 변수를 사용합니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>매개 변수 우선 순위

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

하지만 외부 매개 변수 파일을 사용하면 인라인 또는 로컬 파일에서 다른 값을 전달할 수 없습니다. **TemplateParameterUri** 매개 변수에서 매개 변수 파일을 지정하는 경우 모든 인라인 매개 변수는 무시됩니다. 외부 파일에서 모든 매개 변수 값을 제공해야 합니다. 템플릿이 매개 변수 파일에 포함할 수 없는 중요한 값을 포함하는 경우 해당 값을 키 자격 증명 모음에 추가하고 동적으로 모든 매개 변수 값을 인라인으로 제공합니다.

### <a name="parameter-name-conflicts"></a>매개 변수 이름 충돌

템플릿에 PowerShell 명령의 매개 변수 중 하나와 이름이 같은 매개 변수가 포함되어 있으면 PowerShell에서 접미사가 **FromTemplate**인 템플릿에서 매개 변수를 제공합니다. 예를 들어 템플릿의 **ResourceGroupName** 매개 변수는 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. **ResourceGroupNameFromTemplate**에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

## <a name="test-template-deployments"></a>템플릿 배포 테스트

리소스를 실제로 배포하지 않고 템플릿과 매개 변수 값을 테스트하려면 [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)를 사용합니다. 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

오류가 감지되지 않으면 명령은 응답 없이 완료됩니다. 오류가 감지되면 명령은 오류 메시지를 반환합니다. 예를 들어, 저장소 계정 SKU에 대해 잘못된 값을 전달하면 다음 오류가 반환됩니다.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

템플릿에 구문 오류가 있는 경우 명령은 템플릿을 구문 분석할 수 없다는 오류를 반환합니다. 메시지는 줄 번호 및 구문 분석 오류의 위치를 나타냅니다.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>다음 단계

- 둘 이상의 지역에서 서비스를 안전하게 출시하려면 [Azure Deployment Manager](deployment-manager-overview.md)를 참조하세요.
- 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.
- SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](resource-manager-powershell-sas-token.md)를 참조하세요.
