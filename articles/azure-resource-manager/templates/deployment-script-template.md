---
title: 템플릿에서 배포 스크립트 사용 | 마이크로 소프트 문서
description: Azure 리소스 관리자 템플릿에서 배포 스크립트를 사용합니다.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: jgao
ms.openlocfilehash: 3ef1c3d3fe0fd1ecad95e027b06ce14fd70d4d3f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437871"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>템플릿에서 배포 스크립트 사용(미리 보기)

Azure 리소스 템플릿에서 배포 스크립트를 사용하는 방법에 대해 알아봅니다. 라는 `Microsoft.Resources/deploymentScripts`새 리소스 형식을 사용 하 고 사용자는 템플릿 배포에서 배포 스크립트를 실행 하 고 실행 결과 검토할 수 있습니다. 이러한 스크립트는 다음과 같은 사용자 지정 단계를 수행하는 데 사용할 수 있습니다.

- 디렉터리에 사용자 추가
- 앱 등록 만들기
- 데이터 평면 작업 수행(예: Blob 또는 시드 데이터베이스 복사)
- 라이센스 키를 조회하고 유효성을 검사합니다.
- 자체 서명된 인증서 만들기
- Azure AD에서 개체 만들기
- 사용자 지정 시스템에서 IP 주소 블록 조회

배포 스크립트의 이점:

- 쉽게 코딩, 사용 및 디버깅할 수 있습니다. 즐겨 찾는 개발 환경에서 배포 스크립트를 개발할 수 있습니다. 스크립트는 템플릿 또는 외부 스크립트 파일에 포함할 수 있습니다.
- 스크립트 언어 및 플랫폼을 지정할 수 있습니다. 현재 Linux 환경에서 Azure PowerShell 및 Azure CLI 배포 스크립트가 지원됩니다.
- 스크립트를 실행하는 데 사용되는 ID를 지정할 수 있습니다. 현재 [Azure 사용자 할당된 관리되는 ID만](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 지원됩니다.
- 명령줄 인수를 스크립트에 전달하도록 허용합니다.
- 스크립트 출력을 지정하고 배포로 다시 전달할 수 있습니다.

> [!IMPORTANT]
> 스크립트를 실행하고 문제를 해결하기 위해 두 개의 배포 스크립트 리소스, 즉 스토리지 계정과 컨테이너 인스턴스가 동일한 리소스 그룹에 만들어집니다. 이러한 리소스는 일반적으로 배포 스크립트 실행이 터미널 상태에 있을 때 스크립트 서비스에서 삭제됩니다. 리소스가 삭제될 때까지 해당 리소스에 대한 요금이 청구됩니다. 자세한 내용은 [배포 스크립트 리소스 정리를](#clean-up-deployment-script-resources)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

- **대상 리소스 그룹에 대한 기여자의 역할을 가진 사용자 할당된 관리 ID입니다.** 이 ID는 배포 스크립트를 실행하는 데 사용됩니다. 리소스 그룹 외부에서 작업을 수행하려면 추가 권한을 부여해야 합니다. 예를 들어 새 리소스 그룹을 만들려는 경우 구독 수준에 ID를 할당합니다.

  > [!NOTE]
  > 배포 스크립트 엔진은 백그라운드에서 저장소 계정과 컨테이너 인스턴스를 만듭니다.  구독이 Azure 저장소 계정(Microsoft.Storage) 및 Azure 컨테이너 인스턴스(Microsoft.ContainerInstance) 리소스 공급자를 등록하지 않은 경우 구독 수준에서 기여자의 역할을 가진 사용자 할당된 관리 ID가 필요합니다.

  ID를 만들려면 Azure 포털을 사용하거나 [Azure CLI를 사용하거나 Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) [PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)을 사용하여 [사용자 할당된 관리되는 ID 만들기를](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)참조하십시오. 이 ID는 템플릿을 배포할 때 필요합니다. ID의 형식은 다음과 같습니다.

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  다음 CLI 또는 PowerShell 스크립트를 사용하여 리소스 그룹 이름과 ID 이름을 제공하여 ID를 가져옵니다.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** 또는 **Azure CLI**. 지원되는 Azure PowerShell 버전 목록은 [여기를](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)참조하십시오. 지원되는 Azure CLI 버전 목록은 [여기를](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)참조하십시오.

    >[!IMPORTANT]
    > 배포 스크립트는 MCR(Microsoft 컨테이너 레지스트리)에서 사용 가능한 CLI 이미지를 사용합니다. 배포 스크립트에 대한 CLI 이미지를 인증하는 데 약 1개월이 걸립니다. 30일 이내에 릴리스된 CLI 버전을 사용하지 마십시오. 이미지의 릴리스 날짜를 찾으려면 [Azure CLI 릴리스 정보](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)입니다. 지원되지 않는 버전을 사용하는 경우 오류 메시지는 지원되는 버전을 나열합니다.

    템플릿을 배포하기 위해 이러한 버전이 필요하지 않습니다. 그러나 이러한 버전은 로컬에서 배포 스크립트를 테스트하는 데 필요합니다. [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 미리 구성된 Docker 이미지를 사용할 수 있습니다.  [개발 환경 구성을](#configure-development-environment)참조하십시오.

## <a name="sample-templates"></a>샘플 템플릿

다음 json이 예입니다.  최신 템플릿 스키마는 [여기에서](/azure/templates/microsoft.resources/deploymentscripts)찾을 수 있습니다.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 이 예제는 데모 용입니다.  **스크립트콘텐츠와** **기본ScriptUris는** 템플릿에 공존할 수 없습니다.

속성 값 세부 정보:

- **ID**: 배포 스크립트 서비스는 사용자가 할당한 관리되는 ID를 사용하여 스크립트를 실행합니다. 현재는 사용자가 할당한 관리되는 ID만 지원됩니다.
- **종류**: 스크립트 유형을 지정합니다. 현재 Azure PowerShell 및 Azure CLI 스크립트가 지원됩니다. 값은 **AzurePowerShell** 및 **AzureCLI입니다.**
- **forceUpdateTag**: 템플릿 배포 간에 이 값을 변경하면 배포 스크립트가 다시 실행됩니다. 매개 변수의 defaultValue로 설정해야 하는 newGuid() 또는 utcNow() 함수를 사용합니다. 자세한 내용은 [스크립트를 두 번 이상 실행](#run-script-more-than-once)을 참조하세요.
- **azPowerShellVersion**/**azCliVersion**: 사용할 모듈 버전을 지정합니다. 지원되는 PowerShell 및 CLI 버전 목록은 [필수 구성 조건](#prerequisites)을 참조하십시오.
- 인수 : 매개 변수 값을 **지정합니다.** 값은 공백으로 구분됩니다.
- 환경변수 : 스크립트에 전달할 환경 변수를 **지정합니다.** 자세한 내용은 [배포 스크립트 개발을](#develop-deployment-scripts)참조하십시오.
- **스크립트콘텐츠**: 스크립트 컨텐더를 지정합니다. 외부 스크립트를 실행하려면 `primaryScriptUri` 대신 사용합니다. 예제에서는 [인라인 스크립트 사용](#use-inline-scripts) 및 [외부 스크립트 사용을](#use-external-scripts)참조하십시오.
- **primaryScriptUri**: 지원되는 파일 확장이 있는 기본 배포 스크립트에 공개적으로 액세스할 수 있는 URL을 지정합니다.
- **지원ScriptUris**: 또는 `ScriptContent` `PrimaryScriptUri`중 하나에서 호출되는 파일을 지원하는 공개적으로 액세스할 수 있는 URL의 배열을 지정합니다.
- **시간 시간 지정**: [ISO 8601 형식으로](https://en.wikipedia.org/wiki/ISO_8601)지정된 최대 허용된 스크립트 실행 시간을 지정합니다. 기본값은 **P1D**입니다.
- **정리기본 설정**. 스크립트 실행이 터미널 상태에 있을 때 배포 리소스를 정리하는 기본 설정을 지정합니다. 기본 설정은 **항상**이며 터미널 상태(성공, 실패, 취소됨)에도 불구하고 리소스를 삭제하는 것을 의미합니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.
- **보존 간격**: 배포 스크립트 실행이 터미널 상태에 도달한 후 서비스가 배포 스크립트 리소스를 유지하는 간격을 지정합니다. 배포 스크립트 리소스는 이 기간이 만료되면 삭제됩니다. 기간은 [ISO 8601 패턴을](https://en.wikipedia.org/wiki/ISO_8601)기반으로 합니다. 기본값은 **P1D이며**이는 7일을 의미합니다. 이 속성은 cleanupPreference가 *OnExpiration*으로 설정된 경우에 사용됩니다. *OnExpiration* 속성은 현재 활성화되어 있지 않습니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.

### <a name="additional-samples"></a>추가 샘플

- [키 자격 증명 모음에 인증서를 만들고 할당합니다.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- 을 [만들고 리소스 그룹에 사용자 할당된 관리되는 ID를 할당하고 배포 스크립트를 실행합니다.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)

> [!NOTE]
> 사용자에게 할당된 ID를 만들고 사전에 권한을 부여하는 것이 좋습니다. 배포 스크립트를 실행하는 동일한 템플릿에서 ID를 만들고 권한을 부여하는 경우 로그인 및 권한 관련 오류가 발생할 수 있습니다. 사용 권한이 효력을 발생하기까지 는 다소 시간이 걸립니다.

## <a name="use-inline-scripts"></a>인라인 스크립트 사용

다음 템플릿에는 형식에 정의된 `Microsoft.Resources/deploymentScripts` 리소스가 하나 있습니다. 강조 표시된 부분은 인라인 스크립트입니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> 인라인 배포 스크립트는 큰따옴표로 묶여 있으므로 배포 스크립트 내의 문자열을 작은따옴표로 묶어야 합니다. PowerShell의 이스케이프 문자는 **&#92;** 입니다. 이전 JSON 샘플에 표시된 대로 문자열 대체를 사용할 수도 있습니다. 이름 매개 변수의 기본값을 참조하십시오.

스크립트는 하나의 매개 변수를 가져와 매개 변수 값을 출력합니다. **배포스크립트출력은 출력을** 저장하는 데 사용됩니다.  출력 섹션에서 **값** 줄은 저장된 값에 액세스하는 방법을 보여 주며 있습니다. `Write-Output`디버깅하는 데 사용됩니다. 출력 파일에 액세스하는 방법을 알아보려면 [배포 스크립트 디버그를](#debug-deployment-scripts)참조하십시오.  속성 설명은 샘플 [템플릿 을](#sample-templates)참조하십시오.

스크립트를 실행하려면 클라우드 셸을 열려고 **시도를** 선택한 다음 다음 코드를 셸 창에 붙여넣습니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

출력은 다음과 같이 표시됩니다.

![리소스 관리자 템플릿 배포 스크립트 안녕하세요 세계 출력](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>외부 스크립트 사용

인라인 스크립트 외에도 외부 스크립트 파일을 사용할 수도 있습니다. **ps1** 파일 확장력이 있는 기본 PowerShell 스크립트만 지원됩니다. CLI 스크립트의 경우 스크립트가 유효한 bash 스크립트인 경우 기본 스크립트에는 확장(또는 확장 없이)이 있을 수 있습니다. 외부 스크립트 파일을 사용하려면 `primaryScriptUri`을 로 바꿉습니다. `scriptContent` 다음은 그 예입니다.

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

예제를 보려면 [여기를](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)선택합니다.

외부 스크립트 파일에 액세스할 수 있어야 합니다.  Azure 저장소 계정에 저장된 스크립트 파일을 보호하려면 [자습서: Azure 리소스 관리자 템플릿 배포의 보안 아티팩트](./template-tutorial-secure-artifacts.md)를 참조하십시오.

## <a name="use-supporting-scripts"></a>지원 스크립트 사용

복잡한 논리를 하나 이상의 지원 스크립트 파일로 구분할 수 있습니다. 이 `supportingScriptURI` 속성을 사용하면 필요한 경우 지원 스크립트 파일에 URI 배열을 제공할 수 있습니다.

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

지원 스크립트 파일은 인라인 스크립트와 기본 스크립트 파일 모두에서 호출할 수 있습니다. 지원 스크립트 파일은 파일 확장에 제한이 없습니다.

지원 파일은 런타임에 azscripts/azscriptinput에 복사됩니다. 상대 경로를 사용하여 인라인 스크립트 및 기본 스크립트 파일의 지원 파일을 참조합니다.

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell 스크립트의 출력으로 작업

다음 템플릿은 두 배포 스크립트 리소스 간에 값을 전달하는 방법을 보여 주며 다음과 같은 방법을 보여 주십니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

첫 번째 리소스에서 **$DeploymentScriptOutputs**라는 변수를 정의 하 고 출력 값을 저장 하는 데 사용 합니다. 템플릿 내의 다른 리소스에서 출력 값에 액세스하려면 다음을 사용합니다.

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>CLI 스크립트의 출력으로 작업

PowerShell 배포 스크립트와 달리 CLI/bash 지원은 스크립트 출력을 저장하기 위해 공통 변수를 노출하지 않고 대신 스크립트 출력 파일이 있는 위치를 저장하는 **AZ_SCRIPTS_OUTPUT_PATH** 환경 변수가 있습니다. 리소스 관리자 템플릿에서 배포 스크립트를 실행하는 경우 Bash 셸에서 이 환경 변수가 자동으로 설정됩니다.

배포 스크립트 출력은 AZ_SCRIPTS_OUTPUT_PATH 위치에 저장되어야 하며 출력은 유효한 JSON 문자열 개체여야 합니다. 파일의 내용은 키-값 쌍으로 저장해야 합니다. 예를 들어 문자열 배열은 {"MyResult": [foo", "bar"] }로 저장됩니다.  배열 결과만 저장하면 [foo", "bar"] 등은 유효하지 않습니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq는](https://stedolan.github.io/jq/) 이전 샘플에서 사용됩니다. 그것은 컨테이너 이미지와 함께 제공. [개발 환경 구성을](#configure-development-environment)참조하십시오.

## <a name="develop-deployment-scripts"></a>배포 스크립트 개발

### <a name="handle-non-terminating-errors"></a>종료되지 않는 오류 처리

배포 스크립트에서 [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) 변수를 사용하여 PowerShell이 종료되지 않는 오류에 응답하는 방법을 제어할 수 있습니다. 배포 스크립트 엔진은 값을 설정/변경하지 않습니다.  $ErrorActionPreference 대해 설정한 값에도 불구하고 배포 스크립트는 스크립트에 오류가 발생할 때 리소스 프로비저닝 상태를 *Failed로* 설정합니다.

### <a name="pass-secured-strings-to-deployment-script"></a>보안 문자열을 배포 스크립트에 전달

컨테이너 인스턴스에서 환경 변수(EnvironmentVariable)를 설정하면 컨테이너에서 실행하는 응용 프로그램 또는 스크립트의 동적 구성을 제공할 수 있습니다. 배포 스크립트는 Azure 컨테이너 인스턴스와 동일한 방식으로 보안되지 않은 환경 변수를 처리합니다. 자세한 내용은 [컨테이너 인스턴스의 환경 변수 설정을](../../container-instances/container-instances-environment-variables.md#secure-values)참조하십시오.

## <a name="debug-deployment-scripts"></a>디버그 배포 스크립트

스크립트 서비스는 스크립트 실행을 위한 [저장소 계정과](../../storage/common/storage-account-overview.md) [컨테이너 인스턴스를](../../container-instances/container-instances-overview.md) 만듭니다. 두 리소스 모두 리소스 이름에 **azscripts** 접미사가 있습니다.

![리소스 관리자 템플릿 배포 스크립트 리소스 이름](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

사용자 스크립트, 실행 결과 및 stdout 파일은 저장소 계정의 파일 공유에 저장됩니다. **azscripts라는 폴더가 있습니다.** 폴더에는 입력 및 출력 파일에 대한 두 개의 폴더가 더 있습니다: **azscriptinput** 및 **azscriptoutput**.

출력 폴더에는 **executionresult.json** 및 스크립트 출력 파일이 포함되어 있습니다. **executionresult.json**에서 스크립트 실행 오류 메시지를 볼 수 있습니다. 출력 파일은 스크립트가 성공적으로 실행될 때만 만들어집니다. 입력 폴더에는 시스템 PowerShell 스크립트 파일과 사용자 배포 스크립트 파일이 포함되어 있습니다. 사용자 배포 스크립트 파일을 수정된 파일로 바꾸고 Azure 컨테이너 인스턴스에서 배포 스크립트를 다시 실행할 수 있습니다.

REST API를 사용하여 리소스 그룹 수준 및 구독 수준에서 배포 스크립트 리소스 배포 정보를 얻을 수 있습니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

다음 예제에서는 [ARMClient를](https://github.com/projectkudu/ARMClient)사용합니다.

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

다음과 유사하게 출력됩니다.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

출력에는 배포 상태와 배포 스크립트 리소스 가 표시됩니다.

다음 REST API는 로그를 반환합니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

배포 스크립트 리소스가 삭제되기 전에만 작동합니다.

포털에서 배포스크립트 리소스를 보려면 **숨겨진 형식 표시를**선택합니다.

![리소스 관리자 템플릿 배포 스크립트, 숨겨진 형식 표시, 포털](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>배포 스크립트 리소스 정리

배포 스크립트는 배포 스크립트를 실행하고 디버그 정보를 저장하는 데 사용되는 저장소 계정과 컨테이너 인스턴스를 만듭니다. 이러한 두 리소스는 프로비저닝된 리소스와 동일한 리소스 그룹에서 만들어지며 스크립트가 만료되면 스크립트 서비스에서 삭제됩니다. 이러한 리소스의 수명 주기를 제어할 수 있습니다.  삭제될 때까지 두 리소스에 대한 요금이 청구됩니다. 가격 정보는 컨테이너 [인스턴스 가격](https://azure.microsoft.com/pricing/details/container-instances/) 및 [Azure 저장소 가격을](https://azure.microsoft.com/pricing/details/storage/)참조하십시오.

이러한 리소스의 수명 주기는 템플릿의 다음 속성에 의해 제어됩니다.

- **정리기본 설정**: 스크립트 실행이 터미널 상태에 있을 때 기본 설정을 정리합니다.  지원되는 값은

  - **항상**: 스크립트 실행이 터미널 상태에 도달하면 리소스를 삭제합니다. 배포스크립트 리소스는 리소스를 정리한 후에도 여전히 존재할 수 있기 때문에 시스템 스크립트는 리소스가 삭제되기 전에 스크립트 실행 결과(예: stdout, 출력, 반환 값 등)를 DB로 복사합니다.
  - **OnSuccess**: 스크립트 실행이 성공한 경우에만 리소스를 삭제합니다. 리소스에 액세스하여 디버그 정보를 찾을 수 있습니다.
  - **OnExpiration**: **보존 간격** 설정이 만료된 경우에만 리소스를 삭제합니다. 이 속성은 현재 사용 안 됩니다.

- **보존 간격**: 스크립트 리소스가 보존될 시간 간격을 지정하고 그 후에 만료되고 삭제됩니다.

> [!NOTE]
> 배포 스크립트 리소스는 다른 용도로 사용하지 않는 것이 좋습니다.

## <a name="run-script-more-than-once"></a>스크립트를 두 번 이상 실행

배포 스크립트 실행은 idempotent 작업입니다. 인라인 스크립트를 포함한 배포 스크립트 리소스 속성이 변경되지 않으면 템플릿을 다시 배포할 때 스크립트가 실행되지 않습니다. 배포 스크립트 서비스는 템플릿의 리소스 이름을 동일한 리소스 그룹의 기존 리소스와 비교합니다. 동일한 배포 스크립트를 여러 번 실행하려는 경우 두 가지 옵션이 있습니다.

- 배포의 이름을 변경스크립트 리소스입니다. 예를 들어 [utcNow](./template-functions-string.md#utcnow) 템플릿 함수를 리소스 이름 또는 리소스 이름의 일부로 사용합니다. 리소스 이름을 변경하면 새 배포Scripts 리소스가 만들어집니다. 스크립트 실행 기록을 유지하는 데 적합합니다.

    > [!NOTE]
    > utcNow 함수는 매개 변수의 기본값에서만 사용할 수 있습니다.

- 템플릿 속성에서 다른 `forceUpdateTag` 값을 지정합니다.  예를 들어 utcNow를 값으로 사용합니다.

> [!NOTE]
> idempotent인 배포 스크립트를 작성합니다. 이렇게 하면 실수로 다시 실행해도 시스템 변경이 발생하지 않습니다. 예를 들어 배포 스크립트가 Azure 리소스를 만드는 데 사용되는 경우 리소스를 만들기 전에 리소스가 존재하지 않는지 확인하여 스크립트가 성공하거나 리소스를 다시 만들지 않도록 합니다.

## <a name="configure-development-environment"></a>개발 환경 구성

미리 구성된 docker 컨테이너 이미지를 배포 스크립트 개발 환경으로 사용할 수 있습니다. 다음 절차에서는 Windows에서 도커 이미지를 구성하는 방법을 보여 주며 있습니다. 리눅스와 맥에 대 한, 인터넷에서 정보를 찾을 수 있습니다.

1. 개발 컴퓨터에 [Docker 데스크톱을](https://www.docker.com/products/docker-desktop) 설치합니다.
1. 도커 바탕 화면을 엽니다.
1. 작업 표시줄에서 Docker 바탕 화면 아이콘을 선택한 다음 **설정을**선택합니다.
1. **공유 드라이브를**선택하고 컨테이너에서 사용할 로컬 드라이브를 선택한 다음 **적용을** 선택합니다.

    ![리소스 관리자 템플릿 배포 스크립트 도커 드라이브](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. 프롬프트에 창 자격 증명을 입력합니다.
1. 명령 프롬프트 또는 Windows PowerShell(PowerShell ISE 를 사용하지 않음)의 터미널 창을 엽니다.
1. 배포 스크립트 컨테이너 이미지를 로컬 컴퓨터로 가져옵니다.

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    이 예제에서는 버전 PowerShell 2.7.0을 사용합니다.

    Microsoft 컨테이너 레지스트리(MCR)에서 CLI 이미지를 가져오려면 다음을 따르면 됩니다.

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    이 예제에서는 버전 CLI 2.0.80을 사용합니다. 배포 스크립트는 [여기에](https://hub.docker.com/_/microsoft-azure-cli)있는 기본 CLI 컨테이너 이미지를 사용합니다.

1. 도커 이미지를 로컬로 실행합니다.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    ** &lt;호스트 드라이버 편지>** 및 ** &lt;호스트 디렉터리 이름>** 공유 드라이브의 기존 폴더로 바꿉니다.  폴더를 컨테이너의 **/data** 폴더에 매핑합니다. 예를 들어 D:\도커를 매핑하려면 다음을 수행합니다.

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **- 컨테이너** 이미지를 살아 있게 유지하는 것을 의미합니다.

    CLI 예제:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 프롬프트가 표시되면 **공유를** 선택합니다.
1. 다음 스크린 샷은 d:\docker 폴더에 helloworld.ps1 파일이 있다는 점을 감안할 때 PowerShell 스크립트를 실행하는 방법을 보여 주었습니다.

    ![리소스 관리자 템플릿 배포 스크립트 도커 cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

스크립트를 성공적으로 테스트한 후 배포 스크립트로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 배포 스크립트를 사용하는 방법을 배웠습니다. 배포 스크립트 자습서를 안내합니다.

> [!div class="nextstepaction"]
> [자습서: Azure 리소스 관리자 템플릿에서 배포 스크립트 사용](./template-tutorial-deployment-script.md)