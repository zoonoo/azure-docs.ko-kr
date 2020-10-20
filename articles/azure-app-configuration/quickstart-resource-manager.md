---
title: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure App Configuration 저장소 만들기
titleSuffix: Azure App Configuration
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure App Configuration 저장소를 만드는 방법을 알아봅니다.
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 09/21/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 840f907015e9673caba46998493b5cb705de5fb7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824188"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure App Configuration 저장소 만들기

이 빠른 시작에서는 다음을 수행하는 방법을 설명합니다.

- ARM 템플릿을 사용하여 App Configuration 저장소 배포
- ARM 템플릿을 사용하여 App Configuration 저장소에서 키-값 만들기
- ARM 템플릿에서 App Configuration 저장소의 키-값 읽기

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/en-us/resources/templates/101-app-configuration-store-kv/)에서 나온 것입니다. 두 개의 키-값이 내부에 있는 새 App Configuration 저장소를 만듭니다. 그런 다음, `reference` 함수를 사용하여 두 개의 키-값 리소스에 대한 값을 출력합니다. 키의 값을 이러한 방식으로 읽으면 템플릿의 다른 위치에서 키를 사용할 수 있습니다.

빠른 시작에서는 `copy` 요소를 사용하여 키-값 리소스의 여러 인스턴스를 만듭니다. `copy` 요소에 대한 자세한 내용은 [ARM 템플릿의 리소스 반복](../azure-resource-manager/templates/copy-resources.md)을 참조하세요.

> [!IMPORTANT]
> 이 템플릿에는 App Configuration 리소스 공급자 버전 `2020-07-01-preview` 이상이 필요합니다. 이 버전에서는 `reference` 함수를 사용하여 키-값을 읽습니다. 이전 버전에서 키-값을 읽는 데 사용된 `listKeyValue` 함수는 `2020-07-01-preview` 버전부터 사용할 수 없습니다.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json" range="1-88" highlight="52-58,61-75,80,84":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): App Configuration 저장소를 만듭니다.
- Microsoft.AppConfiguration/configurationStores/keyValues: 키-값을 App Configuration 저장소 내에 만듭니다.

> [!NOTE]
> `keyValues` 리소스의 이름은 키와 레이블의 조합입니다. 키와 레이블은 `$` 구분 기호로 조인됩니다. 레이블은 선택 사항입니다. 위의 예제에서 이름이 `myKey`인 `keyValues` 리소스는 레이블이 없는 키-값을 만듭니다.
>
> URL 인코딩이라고도 하는 퍼센트 인코딩을 사용하면 ARM 템플릿 리소스 이름에 허용되지 않는 문자를 키 또는 레이블에 포함할 수 있습니다. `%`도 허용되는 문자가 아니므로 `~`가 대신 사용됩니다. 이름을 올바르게 인코딩하려면 다음 단계를 수행합니다.
>
> 1. URL 인코딩을 적용합니다.
> 2. `~`을 `~7E`로 바꿉니다.
> 3. `%`을 `~`로 바꿉니다.
>
> 예를 들어 키 이름이 `AppName:DbEndpoint`이고 레이블 이름이 `Test`인 키-값 쌍을 만들려면 리소스 이름이 `AppName~3ADbEndpoint$Test`여야 합니다.

## <a name="deploy-the-template"></a>템플릿 배포

다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 두 개의 키-값이 내부에 있는 App Configuration 저장소를 만듭니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

다음 PowerShell cmdlet을 사용하여 템플릿을 배포할 수도 있습니다. 키-값은 PowerShell 콘솔의 출력에 있습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure 포털](https://portal.azure.com)
1. Azure Portal 검색 상자에서 **App Configuration**을 입력합니다. 목록에서 **App Configuration**을 선택합니다.
1. 새로 만든 App Configuration 리소스를 선택합니다.
1. **작업** 아래에서 **구성 탐색기**를 클릭합니다.
1. 두 개의 키-값이 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, App Configuration 저장소 및 모든 관련 리소스를 삭제합니다. 나중에 App Configuration 저장소를 사용하려는 경우 삭제를 건너뛸 수 있습니다. 이 저장소를 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>다음 단계

Azure App Configuration을 사용하여 다른 애플리케이션을 만드는 방법에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)