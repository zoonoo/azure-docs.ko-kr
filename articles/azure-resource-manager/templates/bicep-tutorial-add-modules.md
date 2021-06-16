---
title: 자습서 - Azure Resource Manager Bicep 파일에 모듈 추가
description: 모듈을 사용하여 원시 리소스 선언의 복잡한 세부 정보를 캡슐화합니다.
author: mumian
ms.date: 03/25/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0149858e4e63e4110af2a18a31b9c66a99ff79c9
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353175"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>자습서: Azure Resource Manager Bicep 파일에 모듈 추가

[이전 자습서](bicep-tutorial-use-parameter-file.md)에서는 매개 변수 파일을 사용하여 Bicep 파일을 배포하는 방법을 알아보았습니다. 이 자습서에서는 Bicep 모듈을 사용하여 원시 리소스 선언의 복잡한 세부 정보를 캡슐화하는 방법에 대해 알아봅니다. 모듈은 솔루션 내에서 공유하고 다시 사용할 수 있습니다.  완료하는 데 **12분** 정도 걸립니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[매개 변수 파일에 대한 자습서](bicep-tutorial-use-parameter-file.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 자습서의 끝 부분에 Bicep 파일의 내용은 다음과 같습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

이 Bicep 파일은 제대로 작동합니다. 그러나 대규모 솔루션의 경우 이러한 모듈을 공유하고 다시 사용할 수 있도록 Bicep 파일을 여러 관련 모듈로 분할하려고 합니다. 현재 Bicep 파일은 스토리지 계정, 앱 서비스 계획 및 웹 사이트를 배포합니다.  스토리지 계정을 모듈로 분리하겠습니다.

## <a name="create-bicep-module"></a>Bicep 모듈 만들기

모든 Bicep 파일을 모듈로 사용할 수 있으므로 모듈을 정의하기 위한 특정 구문이 없습니다. 다음 내용이 포함된 _storage.bicep_ 파일을 만듭니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

이 모듈에는 스토리지 계정 리소스와 관련 매개 변수 및 변수가 포함되어 있습니다. _location_ 매개 변수 및 _resourceTags_ 매개 변수의 값이 제거되었습니다. 이러한 값은 기본 Bicep 파일에서 전달됩니다.

## <a name="consume-bicep-module"></a>Bicep 모듈 사용

기존 _azuredeploy.bicep_ 의 스토리지 계정 리소스 정의를 다음 Bicep 콘텐츠로 바꿉니다.

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **모듈**: 키워드.
- **기호화된 이름**(stg): 모듈의 식별자입니다.
- **모듈 파일**: 이 예제에서 모듈에 대한 경로는 상대 경로(./storage.bicep)를 사용하여 지정합니다. 일관된 컴파일 플랫폼 간 사용을 위해 슬래시(/) 디렉터리 구분 기호를 사용하여 Bicep의 모든 경로를 지정해야 합니다. Windows 백슬래시(\) 문자는 지원되지 않습니다.

스토리지 엔드포인트를 검색하려면 _azuredeploy.bicep_ 의 출력을 다음 Bicep으로 업데이트합니다.

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

완료된 azuredeploy.bicepazuredeploy에는 다음 콘텐츠가 있습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>템플릿 배포

Azure CLI 또는 Azure PowerShell을 사용하여 템플릿을 배포합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 `bicepFile` 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---
> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 이 자습서에는 배포한 새 리소스 그룹이 표시됩니다.
1. 리소스 그룹을 선택하고 배포된 리소스를 살펴봅니다. 템플릿 파일에서 지정한 값과 일치하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다. 이 시리즈를 완료했으면 삭제할 세 개의 리소스 그룹(**myResourceGroup**, **myResourceGroupDev** 및 **myResourceGroupProd**)이 있습니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

축하합니다. Azure에 Bicep 파일을 배포하는 소개를 마쳤습니다. 의견이나 제안 사항이 있으면 사용자 의견 섹션에서 알려주십시오. 감사합니다.

다음 자습서 시리즈는 템플릿 배포에 대해 자세히 설명합니다.

> [!div class="nextstepaction"]
> [로컬 템플릿 배포](./deployment-tutorial-local-template.md)
