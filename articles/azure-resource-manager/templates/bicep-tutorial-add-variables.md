---
title: 자습서 - Azure Resource Manager Bicep 파일에 변수 추가
description: Bicep 파일에 변수를 추가하여 구문을 단순화합니다.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632478"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>자습서: Azure Resource Manager Bicep 파일에 변수 추가

이 자습서에서는 Bicep 파일에 변수를 추가하는 방법을 알아봅니다. 변수는 식을 한 번 작성하고 Bicep 파일 전체에서 다시 사용할 수 있도록 하여 Bicep 파일을 간소화합니다. 이 자습서를 완료하는 데 **7분** 이 소요됩니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[함수에 대한 자습서](bicep-tutorial-add-functions.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 자습서의 끝 부분에 Bicep 파일의 내용은 다음과 같습니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

스토리지 계정 이름에는 고유한 이름을 제공해야 하므로 매개 변수를 사용하기 어렵습니다. 이 시리즈의 이전 자습서를 완료한 경우 고유한 이름을 추측하는 데 질렸을 것입니다. 스토리지 계정의 고유한 이름을 생성하는 변수를 추가하면 이 문제를 해결할 수 있습니다.

## <a name="use-variable"></a>변수 사용

다음 예제는 고유한 스토리지 계정 이름을 만드는 Bicep 파일에 변수를 추가하기 위한 변경 사항을 보여줍니다. 전체 파일을 복사하고 Bicep 파일을 해당 콘텐츠로 바꿉니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

`uniqueStorageName`이라는 변수가 포함되어 있는 것을 확인하세요. 이 변수는 세 개의 함수를 사용하여 문자열 값을 생성합니다.

[resourceGroup](template-functions-resource.md#resourcegroup) 함수에 익숙할 것입니다. 이 경우 이전 자습서에 표시된 것처럼 `location` 속성 대신 `id` 속성을 가져옵니다. `id` 속성은 구독 ID 및 리소스 그룹 이름을 포함한 리소스 그룹의 전체 식별자를 반환합니다.

[uniqueString](template-functions-string.md#uniquestring) 함수는 13자 해시 값을 만듭니다. 반환되는 값은 전달하는 매개 변수에 따라 달라집니다. 이 자습서에서는 해시 값에 대한 입력으로 리소스 그룹 ID를 사용합니다. 즉, 이 Bicep 파일을 여러 리소스 그룹에 배포하고 다른 고유한 문자열 값을 가져올 수 있습니다. 그러나 동일한 리소스 그룹에 배포하는 경우에는 동일한 값을 얻을 수 있습니다.

Bicep은 [문자열 보간](https://en.wikipedia.org/wiki/String_interpolation#) 구문을 지원합니다. 이 변수의 경우 매개 변수의 문자열과 `uniqueString` 함수의 문자열을 가져와서 하나의 문자열로 결합합니다.

`storagePrefix` 매개 변수를 사용하면 스토리지 계정을 식별하는 데 도움이 되는 프리픽스를 전달할 수 있습니다. 긴 리소스 목록에서 배포한 후 스토리지 계정을 더욱 쉽게 식별할 수 있도록 고유한 명명 규칙을 만들 수 있습니다.

마지막으로, 이제 스토리지 이름이 매개 변수 대신 변수로 설정되었는지 확인합니다.

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Bicep 파일을 배포하겠습니다. 스토리지 이름의 접두사만 제공하기 때문에 이 Bicep 파일을 이전 Bicep 파일보다 쉽게 배포할 수 있습니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 `bicepFile` 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 스토리지 계정 리소스가 배포된 것을 볼 수 있습니다. 스토리지 계정의 이름은 **store** 에 임의의 문자 문자열을 더한 값입니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 스토리지 계정의 고유한 이름을 만드는 변수를 추가했습니다. 다음 자습서에서는 배포된 스토리지 계정에서 값을 반환합니다.

> [!div class="nextstepaction"]
> [출력 추가](bicep-tutorial-add-outputs.md)
