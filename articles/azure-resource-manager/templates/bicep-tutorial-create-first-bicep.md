---
title: 자습서 - Azure Resource Manager Bicep 파일 만들기 및 배포
description: Azure 리소스를 배포하기 위한 첫 번째 Bicep 파일을 만듭니다. 이 자습서에서는 Bicep 파일 구문 및 스토리지 계정을 배포하는 방법에 대해 알아봅니다.
author: mumian
ms.date: 04/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 953ca06c3f59f1b39b7b5a5d99ef38814b97c565
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353156"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>자습서: 첫 번째 Azure Resource Manager Bicep 파일 만들기 및 배포

이 자습서에서는 [Bicep](./bicep-overview.md)에 대해 소개합니다. 시작 템플릿을 만들어 Bicep 파일에 배포하는 방법을 보여줍니다. Bicep 파일의 구조와 Bicep 파일 작업에 필요한 도구에 대해 알아봅니다. 이 자습서를 완료하는 데 약 **12분** 정도 걸리지만, 실제 시간은 설치해야 하는 도구 수에 따라 달라집니다.

이 자습서는 시리즈의 첫 번째 자습서입니다. 시리즈를 진행하면서 Bicep 파일의 모든 핵심 부분을 살펴볼 때까지 시작 Bicep 파일을 단계별로 수정합니다. 이러한 요소는 훨씬 더 복잡한 Bicep 파일의 구성 요소입니다. 시리즈가 끝날 때쯤에는 자신만의 고유한 Bicep 파일을 만들고, 이 Bicep 파일을 사용하여 배포를 자동화할 준비가 됩니다.

Bicep 파일을 사용하면 얻을 수 있는 이점과 Bicep 파일을 사용하여 배포를 자동화해야 하는 이유에 대해 알아보려면 [Bicep](./bicep-overview.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>도구 가져오기

먼저 Bicep 파일을 만들고 배포하는 데 필요한 도구가 있는지 확인합니다. 이러한 도구를 로컬 머신에 설치합니다.

### <a name="editor"></a>편집기

Bicep 파일을 만들려면 좋은 편집기가 필요합니다. Bicep 확장이 포함된 Visual Studio Code를 권장합니다. 이러한 도구를 설치해야 하는 경우 [Bicep 개발 도구 구성](./bicep-install.md#development-environment)을 참조하세요.

### <a name="command-line-deployment"></a>명령줄 배포

Azure CLI 또는 Azure PowerShell을 사용하여 Bicep 파일을 배포할 수 있습니다. Azure CLI의 경우 버전 2.20.0 이상이 필요합니다. Azure PowerShell의 경우 버전 5.6.0 이상 필요합니다. 설치 지침은 다음을 참조하세요.

- [Azure PowerShell 설치](/powershell/azure/install-az-ps)
- [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
- [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
- [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)

Azure PowerShell 또는 Azure CLI가 설치되면 처음으로 로그인해야 합니다. 도움을 받으려면 [로그인 - PowerShell](/powershell/azure/install-az-ps#sign-in) 또는 [로그인 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)를 참조하세요.

이제 Bicep 파일에 대한 학습을 시작할 준비가 되었습니다.

## <a name="create-your-first-bicep-file"></a>첫 번째 Bicep 파일 만들기

1. Bicep 확장이 설치된 Visual Studio Code를 엽니다.
1. **파일** 메뉴에서 **새 파일** 을 선택하여 새 파일을 만듭니다.
1. **파일** 메뉴에서 **다른 이름으로 저장** 을 선택합니다.
1. 파일 이름을 _azuredeploy_ 로 지정하고, _bicep_ 파일 확장명을 선택합니다. 파일의 전체 이름은 _azuredeploy.bicep_ 입니다.
1. 파일을 워크스테이션에 저장합니다. 나중에 Bicep 확장을 배포할 때 해당 경로를 제공하므로 기억하기 쉬운 경로를 선택합니다.
1. 다음 내용을 파일에 붙여 넣습니다.

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'  // must be globally unique
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Visual Studio Code 환경은 다음과 같습니다.

    ![Visual Studio Code 첫 번째 Bicep 파일](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    리소스 선언에는 다음 네 가지 구성 요소가 있습니다.

    - **리소스**: 키워드
    - **기호 이름**(stg): 기호 이름은 bicep 파일 전체에서 리소스를 참조하는 식별자입니다. 배포되는 리소스의 이름이 아닙니다. 리소스 이름은 **name** 속성으로 정의됩니다.  이 목록에서 네 번째 구성 요소를 참조하세요. 자습서를 쉽게 따라 할 수 있도록 이 자습서 시리즈에서는 스토리지 계정 리소스의 기호 이름으로 **stg** 를 사용합니다. 기호 이름을 사용하여 개체 속성의 전체 목록을 가져오는 방법을 보려면 [출력 추가](./bicep-tutorial-add-outputs.md)를 참조하세요.
    - **리소스 유형**(Microsoft.Storage/storageAccounts@2019-06-01): 리소스 공급자(Microsoft.Storage), 리소스 유형(storageAccounts) 및 apiVersion(2019-06-01)으로 구성됩니다. 각 리소스 공급자는 자체 API 버전을 게시하므로 이 값은 유형에 따라 다릅니다. [ARM 템플릿 참조](/azure/templates/)에서 다양한 Azure 리소스에 대한 더 많은 유형과 apiVersion을 찾을 수 있습니다.
    - **속성**(내부의 모든 항목 = {...}): 주어진 리소스 종류에 대해 지정하려는 특정 속성입니다. 이러한 속성은 ARM 템플릿에서 사용할 수 있는 것과 정확히 같은 속성입니다. 모든 리소스에는 `name` 속성이 있습니다. 대부분의 리소스에는 `location` 속성이 있으며, 이 속성은 리소스가 배포되는 지역을 설정합니다. 다른 속성은 리소스 유형 및 API 버전에 따라 다릅니다. API 버전과 사용 가능한 속성 간의 연결을 이해하는 것이 중요하므로 좀 더 자세히 살펴보겠습니다.

        이 스토리지 계정의 경우 [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts)에서 해당 API 버전을 확인할 수 있습니다. Bicep 파일에 모든 속성을 추가하지는 않았습니다. 많은 속성은 선택 사항입니다. `Microsoft.Storage` 리소스 공급자가 새로운 API 버전을 출시할 수 있지만, 배포 중인 버전을 변경할 필요는 없습니다. 해당 버전을 계속 사용하고 배포 결과가 일관적이라는 것을 확인할 수 있습니다.

        [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)과 같은 이전 API 버전이 보이는 경우, 더 작은 속성 집합을 사용할 수 있습니다.

        리소스의 API 버전을 변경하려는 경우에는, 해당 버전의 속성을 평가하고 Bicep 파일을 적절히 조정해야 합니다.

    자세한 내용은 [Bicep 구조체](./bicep-file.md)를 참조하세요.

    이름 속성에 대한 설명이 있습니다.  한 줄 주석에는 `//`를 사용하고 여러 줄 주석에는 `/* ... */`를 사용합니다.

1. 중괄호 `{}`를 포함하여 `{provide-unique-name}`을 고유한 스토리지 계정 이름으로 바꿉니다.

    > [!IMPORTANT]
    > 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 이름에는 소문자 또는 숫자만 사용할 수 있습니다. 24자 이하여야 합니다. **store1** 을 접두사로 사용하고 이니셜과 오늘 날짜를 추가하는 이름 지정 패턴을 시도해 볼 수 있습니다. 예를 들어 **store1abc09092019** 와 같은 이름을 사용할 수 있습니다.

    스토리지 계정에 대한 고유 이름을 추측하는 것은 쉽지 않으며 대량 배포를 자동화하는 데 적합하지 않습니다. 이 자습서 시리즈의 뒷부분에서 고유한 이름을 쉽게 만들 수 있는 Bicep 기능을 사용합니다.

1. 파일을 저장합니다.

축하합니다. 첫 번째 Bicep 파일을 만들었습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure PowerShell/Azure CLI를 사용하여 시작하려면 Azure 자격 증명으로 로그인합니다.

다음 코드 섹션에서 탭을 선택하여 Azure PowerShell과 Azure CLI 중 하나를 선택합니다. 이 문서의 CLI 예제는 Bash 셸에 대해 작성되었습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다. `[SubscriptionID/SubscriptionName]` 및 대괄호 `[]`을(를) 구독 정보로 바꿉니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>리소스 그룹 만들기

Bicep 파일을 배포하는 경우 리소스를 포함할 리소스 그룹을 지정합니다. 배포 명령을 실행하기 전에 먼저 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Bicep은 Azure Resource Manager 템플릿(ARM 템플릿)에 대한 투명한 추상화입니다. 각 Bicep 파일은 배포되기 전에 표준 ARM 템플릿으로 컴파일됩니다. Bicep 파일을 배포하기 전에 ARM 템플릿으로 컴파일하거나 Bicep 파일을 직접 배포할 수 있습니다. Bicep 파일을 배포하려면 Azure CLI 또는 Azure PowerShell을 사용합니다. 만든 리소스 그룹을 사용합니다. 배포 이름은 배포 기록에서 쉽게 식별할 수 있도록 지정합니다. 편의를 위해 Bicep 파일의 경로를 저장하는 변수도 만듭니다. 해당 변수를 사용하면 배포할 때마다 경로를 다시 입력할 필요가 없기 때문에 배포 명령을 더 쉽게 실행할 수 있습니다. 중괄호 `{}`를 포함하는 `{provide-the-path-to-the-bicep-file}`를 _.bicep_ 파일 확장명이 있는 Bicep 파일의 경로로 바꿉니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

배포 명령에서 결과를 반환합니다. `ProvisioningState`를 찾아서 배포에 성공했는지 확인합니다.

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

1. 마지막 절차에서 리소스 그룹 배포를 선택합니다. 기본 이름은 **myResourceGroup** 입니다. 리소스 그룹 내에 배포된 리소스는 표시되지 않습니다.

1. 개요의 오른쪽 위에 배포 상태가 표시됩니다. **1 성공** 을 선택합니다.

   ![배포 상태 보기](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. 리소스 그룹에 대한 배포 기록이 표시됩니다. **firstbicep** 을 선택합니다.

   ![배포 선택](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. 배포 요약이 표시됩니다. 하나의 스토리지 계정이 배포되어 있습니다. 왼쪽에서는 배포 중에 사용되는 입력, 출력 및 템플릿을 볼 수 있습니다.

   ![배포 요약 보기](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우 리소스 그룹을 삭제하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure에 배포할 간단한 Bicep 파일을 만들었습니다.  이후 자습서에서는 매개 변수, 변수, 출력 및 모듈을 Bicep 파일에 추가하는 방법을 알아봅니다. 이러한 기능은 훨씬 더 복잡한 Bicep 파일을 구성하는 요소입니다.

> [!div class="nextstepaction"]
> [매개 변수 추가](bicep-tutorial-add-parameters.md)
