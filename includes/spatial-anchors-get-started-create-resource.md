---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628722"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors 리소스 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

<a href="https://portal.azure.com" target="_blank">Azure 포털</a>로 이동합니다.

왼쪽 창에서 **리소스 만들기** 를 선택합니다.

검색 상자를 사용하여 **Spatial Anchors** 를 검색합니다.

![Spatial Anchors 검색 결과를 보여주는 스크린샷.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**Spatial Anchors** 를 선택한 다음, **만들기** 를 선택합니다.

**Spatial Anchors 계정** 창에서 다음을 수행합니다.

* 일반 영숫자 문자를 사용하여 고유한 리소스 이름을 입력합니다.
* 리소스를 연결할 구독을 선택합니다.
* **새로 만들기** 를 선택하여 리소스 그룹을 만듭니다. 이름을 **myResourceGroup** 으로 지정한 다음, **확인** 을 선택합니다.

  [!INCLUDE [resource group intro text](resource-group.md)]

* 리소스를 배치할 위치(Azure 지역)를 선택합니다.
* **새로 만들기** 를 선택하여 리소스 만들기를 시작합니다.

![리소스를 만들기 위한 Spatial Anchors 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

리소스를 만든 후 Azure Portal은 배포가 완료되었음을 표시합니다.

![리소스 배포가 완료되었음을 보여주는 스크린샷입니다.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

**리소스로 이동** 을 선택합니다. 이제 리소스 속성을 볼 수 있습니다.

나중에 사용하기 위해 리소스의 **계정 ID** 값을 텍스트 편집기에 복사합니다.

![리소스 속성 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

또한 리소스의 **계정 도메인** 값을 텍스트 편집기에 복사합니다.

![리소스의 계정 도메인 값을 보여주는 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

**설정** 아래에서 **키** 를 선택합니다. 나중에 사용하기 위해 **기본 키** 값, **계정 키** 를 텍스트 편집기에 복사합니다.

![계정에 대한 키 창의 스크린샷.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. 로그인 후 [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 공간 앵커 계정을 설정할 구독을 선택합니다.

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   [az spatial-anchors-account list](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) 명령을 사용하여 리소스 그룹의 현재 공간 앵커 계정을 볼 수 있습니다.

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   또한 구독의 공간 앵커 계정을 볼 수도 있습니다.

   ```azurecli
   az spatial-anchors-account list
   ```

1. [az spatial-anchors-account create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) 명령을 실행하여 공간 앵커 계정을 만듭니다.

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. [az spatial-anchors-account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) 명령을 사용하여 리소스 속성을 봅니다.

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   나중에 사용하기 위해 리소스 **계정 ID** 값과 리소스 **계정 도메인** 값을 텍스트 편집기에 복사합니다.

1. [az spatial-anchors-account key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) 명령을 실행하여 기본 키 및 보조 키를 가져옵니다.

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   나중에 사용하기 위해 키 값을 텍스트 편집기에 복사합니다.

   키를 다시 생성해야 하는 경우 [az spatial-anchors-account key renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) 명령을 사용합니다.

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

[az spatial-anchors-account delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) 명령을 사용하여 계정을 삭제할 수 있습니다.

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell에 대한 환경을 준비하는 것으로 시작합니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> **Az.MixedReality** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. 로그인 후 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 공간 앵커 계정을 설정할 구독을 선택합니다.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) cmdlet을 사용하여 리소스 그룹의 현재 공간 앵커 계정을 볼 수 있습니다.

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   또한 구독의 공간 앵커 계정을 볼 수도 있습니다.

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) cmdlet을 실행하여 공간 앵커 계정을 만듭니다.

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) cmdlet을 사용하여 리소스 속성을 봅니다.

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   나중에 사용할 수 있도록 **accountId** 속성 값과 **accountDomain** 속성 값을 텍스트 편집기에 복사합니다.

1. [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) cmdlet을 실행하여 기본 키 및 보조 키를 가져옵니다.

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   나중에 사용하기 위해 키 값을 텍스트 편집기에 복사합니다.

   키를 다시 생성해야 하는 경우 [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

[Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) cmdlet을 사용하여 계정을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
