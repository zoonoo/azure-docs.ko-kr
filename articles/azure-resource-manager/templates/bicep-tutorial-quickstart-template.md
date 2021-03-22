---
title: 자습서 - Azure Resource Manager Bicep 개발에 빠른 시작 템플릿 사용
description: Azure 빠른 시작 템플릿을 사용하여 Bicep 개발을 완료하는 방법을 알아봅니다.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 12dcf4bfc00c299d94d45a5bd02bf9eea576cc73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742878"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>자습서: Azure Resource Manager Bicep 개발에 Azure 빠른 시작 템플릿 사용

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 커뮤니티 제공 JSON 템플릿의 리포지토리입니다. Bicep 개발에 샘플 템플릿을 사용할 수 있습니다. 이 자습서에서는 웹 사이트 리소스 정의를 찾아 Bicep으로 디컴파일하고 Bicep 파일에 추가합니다. 완료하는 데 **12분** 정도 걸립니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[내보낸 템플릿에 대한 자습서](bicep-tutorial-export-template.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 자습서의 끝 부분에 Bicep 파일의 내용은 다음과 같습니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

이 Bicep 파일은 스토리지 계정 및 앱 서비스 계획을 배포하는 데 사용되지만 여기에 웹 사이트를 추가할 수도 있습니다. 미리 빌드된 템플릿을 사용하여 리소스를 배포하는 데 필요한 JSON을 신속하게 검색할 수 있습니다. Azure 빠른 시작 템플릿에서 Bicep 샘플을 제공하기 전에 변환 도구를 사용하여 JSON 템플릿을 Bicep 파일로 변환할 수 있습니다.

## <a name="find-template"></a>템플릿 찾기

현재 Azure 빠른 시작 템플릿은 JSON 템플릿만 제공합니다. JSON 템플릿을 Bicep 템플릿으로 디컴파일하는 데 사용할 수 있는 도구가 있습니다.

1. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)을 엽니다.
1. **검색** 에 _deploy linux web app_(Linux 웹앱 배포)을 입력합니다.
1. 제목이 **기본 Linux 웹앱 배포** 인 타일을 선택합니다. 찾는 데 문제가 있으면 [직접 링크](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)를 사용하세요.
1. **GitHub에서 찾아보기** 를 선택합니다.
1. _azuredeploy.json_ 을 선택합니다. 사용할 수 있는 템플릿입니다.
1. **Raw** 를 선택한 다음, URL의 복사본을 만듭니다.
1. **https://bicepdemo.z22.web.core.windows.net/** 으로 이동하여 **디컴파일** 을 선택한 다음, 원시 템플릿 URL을 제공합니다.
1. Bicep 템플릿을 검토합니다. 특히, `Microsoft.Web/sites` 리소스를 살펴봅니다.

    ![Resource Manager 템플릿 빠른 시작 웹 사이트](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    JSON 템플릿에 대해 작업한 경우 이 새 리소스에서 몇 가지 중요한 Bicep 기능이 제공됩니다.

    ARM JSON 템플릿에서는 _dependsOn_ 속성을 사용하여 리소스 종속성을 수동으로 지정해야 합니다. 웹 사이트 리소스는 앱 서비스 계획 리소스에 따라 달라집니다. Bicep을 통해 기호화된 이름을 사용하여 리소스의 속성을 참조하는 경우 dependsOn 속성이 자동으로 추가됩니다.

    컴파일된 JSON 템플릿에서 resourceId(...) 함수로 변환될 앱 서비스 계획(appServicePlanName.id)의 기호화된 이름에서 리소스 ID를 쉽게 참조할 수 있습니다.

## <a name="revise-existing-bicep-file"></a>기존 Bicep 파일 수정

디컴파일된 빠른 시작 템플릿을 기존 Bicep 파일과 병합합니다. 이전 자습서에서 수행한 것과 마찬가지로, 명명 규칙과 일치하도록 리소스 기호 이름 및 리소스 이름을 업데이트합니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

웹앱 이름은 Azure에서 고유해야 합니다. 이름이 중복되지 않도록, `webAppPortalName` 변수가 `var webAppPortalName_var = '${webAppName}-webapp'` 에서 `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` 으로 업데이트되었습니다.

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Azure CLI 또는 Azure PowerShell을 사용하여 Bicep 템플릿을 배포합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 **bicepFile** 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Bicep 개발에 빠른 시작 템플릿을 사용하는 방법을 알아보았습니다. 다음 자습서에서는 리소스에 태그를 추가합니다.

> [!div class="nextstepaction"]
> [태그 추가](bicep-tutorial-add-tags.md)
