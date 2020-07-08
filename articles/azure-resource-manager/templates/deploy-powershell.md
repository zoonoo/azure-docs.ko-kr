---
title: PowerShell 및 템플릿을 사용 하 여 리소스 배포
description: Azure Resource Manager 및 Azure PowerShell를 사용 하 여 Azure에 리소스를 배포 합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: af255e0248c029f42c9c2999ae7c0389d60c58fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431847"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>ARM 템플릿을 사용 하 여 리소스 배포 및 Azure PowerShell

ARM (Azure Resource Manager) 템플릿과 함께 Azure PowerShell를 사용 하 여 Azure에 리소스를 배포 하는 방법을 알아봅니다. Azure 솔루션 배포 및 관리의 개념에 대 한 자세한 내용은 [템플릿 배포 개요](overview.md)를 참조 하세요.

## <a name="deployment-scope"></a>배포 범위

리소스 그룹, 구독, 관리 그룹 또는 테 넌 트에 대 한 배포를 대상으로 지정할 수 있습니다. 대부분의 경우 리소스 그룹에 대 한 배포를 대상으로 합니다. 더 큰 범위에서 정책 및 역할 할당을 적용 하려면 구독, 관리 그룹 또는 테 넌 트 배포를 사용 합니다. 구독에 배포 하는 경우 리소스 그룹을 만들고 여기에 리소스를 배포할 수 있습니다.

배포의 범위에 따라 다른 명령을 사용합니다.

* **리소스 그룹**에 배포 하려면 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용 합니다.

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* **구독**에 배포 하려면 AzSubscriptionDeployment를 사용 합니다.

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  구독 수준 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)를 참조하세요.

* **관리 그룹**에 배포 하려면 [AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)를 사용 합니다.

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  관리 그룹 수준 배포에 대한 자세한 내용은 [관리 그룹 수준에서 리소스 만들기](deploy-to-management-group.md)를 참조하세요.

* **테 넌 트**에 배포 하려면 [AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)를 사용 합니다.

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  테넌트 수준 배포에 대한 자세한 내용은 [테넌트 수준에서 리소스 만들기](deploy-to-tenant.md)를 참조하세요.

이 문서의 예제에서는 리소스 그룹 배포를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

배포할 템플릿이 필요 합니다. 아직 없는 경우 Azure 빠른 시작 템플릿 리포지토리에서 [예제 템플릿을](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) 다운로드 하 고 저장 합니다. 이 문서에 사용된 로컬 파일 이름은 **c:\MyTemplates\azuredeploy.json**입니다.

Azure Cloud Shell를 사용 하 여 템플릿을 배포 하지 않는 한 Azure PowerShell를 설치 하 고 Azure에 연결 해야 합니다.

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

ARM 템플릿을 로컬 컴퓨터에 저장 하는 대신 외부 위치에 저장 하는 것이 좋습니다. 원본 제어 리포지토리(예: GitHub)에 템플릿을 저장할 수 있습니다. 또는 조직에서 공유 액세스에 대한 Azure Storage 계정에 저장할 수 있습니다.

외부 템플릿을 배포 하려면 템플릿 **uri** 매개 변수를 사용 합니다. 예제의 URI를 사용하여 GitHub에서 샘플 템플릿을 배포합니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

앞의 예제에서는 템플릿에 중요한 데이터가 포함되어 있지 않으므로 대부분의 시나리오에 적합한 이 템플릿에 대해 공개적으로 액세스할 수 있는 URI가 필요합니다. 중요한 데이터(예: 관리자 암호)를 지정해야 하는 경우 해당 값을 안전한 매개 변수로 전달합니다. 그러나 템플릿에 공개적으로 액세스할 수 있도록 하지 않으려면 프라이빗 스토리지 컨테이너에 저장하여 보호할 수 있습니다. SAS(공유 액세스 서명) 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요. 자습서를 진행 하려면 [자습서: ARM 템플릿 배포의 Azure Key Vault 통합](template-tutorial-use-key-vault.md)을 참조 하세요.

## <a name="preview-changes"></a>변경 내용 미리 보기

템플릿을 배포 하기 전에 템플릿이 사용자 환경에 적용 되는 변경 내용을 미리 볼 수 있습니다. [가상 작업](template-deploy-what-if.md) 을 사용 하 여 템플릿이 필요한 변경을 수행 하는지 확인 합니다. 이 경우에서 템플릿의 오류도 확인 합니다.

## <a name="deploy-from-azure-cloud-shell"></a>Azure Cloud Shell에서 배포

[Azure Cloud Shell](https://shell.azure.com)을 사용하여 템플릿을 배포할 수 있습니다. 외부 템플릿을 배포하려면 템플릿의 URI를 제공합니다. 로컬 템플릿을 배포하려면 먼저 Cloud Shell용 스토리지 계정에 템플릿을 로드해야 합니다. 셸에 파일을 업로드하려면 셸 창에서 **파일 업로드/다운로드** 메뉴 아이콘을 선택합니다.

Cloud Shell를 열려면 [https://shell.azure.com](https://shell.azure.com) 다음 코드 섹션에서 **시도** 를 찾아보거나 선택 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

셸에 코드를 붙여넣으려면 셸 내부를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

## <a name="pass-parameter-values"></a>매개 변수 값 전달

매개 변수 값을 전달하려면 인라인 매개 변수 또는 매개 변수 파일을 사용할 수 있습니다.

### <a name="inline-parameters"></a>인라인 매개 변수입니다.

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

파일에서 매개 변수 값을 가져오면 구성 값을 제공해야 하는 경우에 유용합니다. 예를 들어, [Linux 가상 머신에 대한 Cloud-Init 값](../../virtual-machines/linux/using-cloud-init.md)을 제공할 수 있습니다.

개체의 배열을 전달 해야 하는 경우 PowerShell에서 해시 테이블을 만들고 배열에 추가 합니다. 배포 하는 동안 해당 배열을 매개 변수로 전달 합니다.

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

매개 변수 파일에 대한 자세한 내용은 [Resource Manager 매개 변수 파일 만들기](parameter-files.md)를 참조하세요.

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

## <a name="next-steps"></a>다음 단계

- 오류 발생 시 성공적인 배포로 롤백하려면 [오류 발생 시 성공적인 배포로 롤백](rollback-on-error.md)을 참조하세요.
- 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의 하는 방법을 이해 하려면 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
- SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 프라이빗 템플릿 배포](secure-template-with-sas-token.md)를 참조하세요.
