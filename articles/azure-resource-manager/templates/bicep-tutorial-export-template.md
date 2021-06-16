---
title: 자습서 - Bicep 개발용 Azure Portal에서 JSON 템플릿 내보내기
description: 내보낸 JSON 템플릿을 사용하여 Bicep 개발을 완료하는 방법을 알아봅니다.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353087"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>자습서: Azure Portal에서 내보낸 JSON 템플릿 사용

이 자습서 시리즈에서는 Azure 스토리지 계정을 배포하는 Bicep 파일을 만들었습니다. 다음 두 자습서에서는 *App Service 계획* 과 *웹 사이트* 를 추가합니다. 템플릿을 처음부터 생성하는 대신 Azure Portal에서 JSON 템플릿을 내보내는 방법과 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 샘플 템플릿을 사용하는 방법을 알아봅니다. 이러한 템플릿을 용도에 맞게 사용자 지정합니다. 이 자습서에서는 템플릿 내보내기 및 Bicep 파일의 결과 사용자 지정에 중점을 둡니다. 완료하는 데 **14분** 정도 걸립니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[출력에 대한 자습서](bicep-tutorial-add-outputs.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 자습서의 끝 부분에 Bicep 파일의 내용은 다음과 같습니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

이 Bicep 파일은 스토리지 계정 배포에 적합하지만 더 많은 리소스를 추가할 수 있습니다. 기존 리소스에서 JSON 템플릿을 내보내서 해당 리소스의 JSON을 빠르게 가져올 수 있습니다. 그런 다음, JSON을 Bicep 파일에 추가하기 전에 Bicep으로 변환합니다.

## <a name="create-app-service-plan"></a>App Service 플랜 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기** 를 선택합니다.
1. **Marketplace 검색** 에 **App Service 계획** 을 입력한 다음, **App Service 계획** 을 선택합니다.  **App Service 계획(클래식)** 을 선택하지 마세요.
1. **만들기** 를 선택합니다.
1. 다음을 입력합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택한 후 이름을 지정합니다. 이 자습서 시리즈에서 사용한 것과 다른 리소스 그룹 이름을 제공합니다.
    - **이름**: App Service 계획의 이름을 입력합니다.
    - **운영 체제**: **Linux** 를 선택합니다.
    - **지역**: Azure 위치를 선택합니다. 예: **미국 중부**
    - **기준 가격**: 비용을 절약하려면 SKU를 **기본 B1**(개발/테스트 중)으로 변경합니다.

    ![Resource Manager 템플릿 내보내기 템플릿 포털](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. **검토 및 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다. 리소스를 만드는 데 몇 분 정도 걸립니다.

## <a name="export-template"></a>템플릿 내보내기

현재 Azure Portal에서는 JSON 템플릿 내보내기만 지원합니다. JSON 템플릿을 Bicep 파일로 디컴파일하는 데 사용할 수 있는 도구가 있습니다.

1. **리소스로 이동** 을 선택합니다.

    ![리소스로 이동](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. **템플릿 내보내기** 를 선택합니다.

    ![Resource Manager 템플릿 내보내기 템플릿](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   템플릿 내보내기 기능은 리소스의 현재 상태를 가져와서 해당 리소스를 배포할 템플릿을 생성합니다. 템플릿을 내보내면 리소스를 배포하는 데 필요한 JSON을 신속하게 가져올 수 있습니다.

1. `Microsoft.Web/serverfarms` 정의와 매개 변수 정의는 필요한 부분입니다.

    ![Resource Manager 템플릿 내보내기 템플릿 내보낸 템플릿](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > 일반적으로 내보낸 템플릿은 템플릿을 만들 때 필요한 것보다 더 자세한 정보를 포함합니다. 예를 들어, 내보낸 템플릿의 SKU 개체에는 5개의 속성이 있습니다. 이 템플릿도 괜찮지만 `name` 속성만 사용할 수 있습니다. 내보낸 템플릿으로 시작한 다음, 요구 사항에 맞게 원하는 대로 수정할 수 있습니다.

1. **다운로드** 를 선택합니다.  다운로드한 zip 파일에는 **template.json** 및 **parameters.json** 이 포함되어 있습니다. 파일의 압축을 풉니다.
1. **https://bicepdemo.z22.web.core.windows.net/** 으로 **디컴파일** 을 선택한 다음, **template.json** 을 엽니다. Bicep에서 템플릿을 가져옵니다.

## <a name="revise-existing-bicep-file"></a>기존 Bicep 파일 수정

내보낸 디컴파일 템플릿에는 필요한 Bicep이 대부분 제공되지만 Bicep 파일에 맞게 사용자 지정해야 합니다. 사용자 Bicep 파일과 내보낸 Bicep 파일 간에 매개 변수와 변수의 차이점에 특히 주의해야 합니다. 물론, 내보내기 프로세스는 Bicep 파일에 이미 정의해 놓은 매개 변수와 변수를 알지 못합니다.

다음 예제는 Bicep 파일에 추가된 내용을 보여줍니다. 여기에는 내보낸 코드와 일부 변경 내용이 포함됩니다. 첫째, 매개 변수 이름을 명명 규칙과 일치하도록 변경합니다. 둘째, 위치 매개 변수를 App Service 계획의 위치에 사용합니다. 셋째, 기본값이 적절한 속성 중 일부를 제거합니다.

전체 파일을 복사하고 Bicep 파일을 해당 콘텐츠로 바꿉니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Azure CLI 또는 Azure PowerShell을 사용하여 Bicep 파일을 배포합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 `bicepFile` 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. 리소스 그룹에는 스토리지 계정과 App Service 계획이 포함됩니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 JSON 템플릿을 내보내는 방법, JSON 템플릿을 Bicep 파일로 변환하는 방법 및 내보낸 템플릿을 Bicep 개발에 사용하는 방법을 알아보았습니다. Azure 빠른 시작 템플릿을 사용하여 Bicep 개발을 간소화할 수도 있습니다.

> [!div class="nextstepaction"]
> [Azure 빠른 시작 템플릿 사용](bicep-tutorial-quickstart-template.md)
