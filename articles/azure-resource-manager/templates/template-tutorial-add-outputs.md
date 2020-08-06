---
title: 자습서 - 템플릿에 출력 추가
description: 구문을 간소화하기 위해 Azure Resource Manager 템플릿에 출력을 추가합니다.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c90f1ce4bc3f3f5ed91bee86bb1f6f9093876c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497786"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>자습서: ARM 템플릿에 출력 추가

이 자습서에서는 ARM(Azure Resource Manager) 템플릿에서 값을 반환하는 방법에 대해 알아봅니다. 배포된 리소스의 값이 필요한 경우 출력을 사용합니다. 이 자습서를 완료하는 데 **7분**이 소요됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[변수에 대한 자습서](template-tutorial-add-variables.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

이전 자습서의 끝 부분에 템플릿에는 다음 JSON이 있습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

스토리지 계정을 배포하지만 스토리지 계정에 대한 정보는 반환하지 않습니다. 나중에 참조할 수 있도록 새 리소스에서 속성을 캡처해야 할 수도 있습니다.

## <a name="add-outputs"></a>출력 추가

출력을 사용하여 템플릿에서 값을 반환할 수 있습니다. 예를 들어 새 스토리지 계정에 대한 엔드포인트를 가져오는 것이 유용할 수 있습니다.

다음 예에는 출력 값을 추가하는 템플릿에 대한 변경 사항이 강조 표시되어 있습니다. 전체 파일을 복사하고 템플릿을 해당 콘텐츠로 바꿉니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

추가한 출력 값과 관련하여 알아두어야 할 몇 가지 중요한 항목이 있습니다.

반환되는 값의 유형은 **object**로 설정됩니다. 즉, JSON 개체를 반환합니다.

[reference](template-functions-resource.md#reference) 함수를 사용하여 스토리지 계정의 런타임 상태를 가져옵니다. 리소스의 런타임 상태를 가져오려면 리소스의 이름 또는 ID를 전달합니다. 이 경우 스토리지 계정의 이름을 만드는 데 사용한 것과 동일한 변수를 사용합니다.

마지막으로, 스토리지 계정에서 **primaryEndpoints** 속성을 반환합니다.

## <a name="deploy-template"></a>템플릿 배포

템플릿을 배포하고 반환되는 값을 확인할 준비가 되었습니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](template-tutorial-create-first-template.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](template-tutorial-create-first-template.md#deploy-template)에 표시된 대로 **templateFile** 변수를 템플릿 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

배포 명령의 출력에서는 출력이 JSON 형식인 경우에만 다음 예제와 유사한 개체가 표시됩니다.

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> 배포에 실패한 경우 배포 명령과 함께 **debug** 스위치를 사용하여 디버그 로그를 표시합니다.  **verbose** 스위치를 사용하여 전체 디버그 로그를 표시할 수도 있습니다.

## <a name="review-your-work"></a>작업 검토

최근 6개의 자습서에서 많은 작업을 수행했습니다. 지금까지 수행한 작업을 검토해 보겠습니다. 쉽게 제공할 수 있는 매개 변수를 사용하여 템플릿을 만들었습니다. 템플릿은 사용자 지정이 가능하고 필요한 값을 동적으로 만들 수 있으므로 여러 환경에서 재사용할 수 있습니다. 또한 스크립트에서 사용할 수 있는 스토리지 계정에 대한 정보도 반환합니다.

이제 리소스 그룹 및 배포 기록을 살펴보겠습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 수행한 단계에 따라 리소스 그룹에 하나 이상의 스토리지 계정이 있을 것입니다.
1. 또한 기록에 몇 가지 성공적인 배포가 나열되어 있을 것입니다. 해당 링크를 선택합니다.

   ![배포 선택](./media/template-tutorial-add-outputs/select-deployments.png)

1. 모든 배포가 기록에 표시됩니다. **addoutputs**라는 배포를 선택합니다.

   ![배포 기록 표시](./media/template-tutorial-add-outputs/show-history.png)

1. 입력을 검토할 수 있습니다.

   ![입력 표시](./media/template-tutorial-add-outputs/show-inputs.png)

1. 출력을 검토할 수 있습니다.

   ![출력 표시](./media/template-tutorial-add-outputs/show-outputs.png)

1. 템플릿을 검토할 수 있습니다.

   ![템플릿 표시](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 템플릿에 반환 값을 추가했습니다. 다음 자습서에서는 템플릿을 내보내고 템플릿에서 내보낸 템플릿의 일부를 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [내보낸 템플릿 사용](template-tutorial-export-template.md)
