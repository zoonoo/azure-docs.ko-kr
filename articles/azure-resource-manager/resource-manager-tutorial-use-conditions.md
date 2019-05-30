---
title: Azure Resource Manager 템플릿에서 조건 사용 | Microsoft Docs
description: 조건에 따라 Azure 리소스를 배포하는 방법을 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0d27eef8ee2c70449bacaced0de89bdc5e6989ff
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357820"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>자습서: Azure Resource Manager 템플릿에서 조건 사용

조건에 따라 Azure 리소스를 배포하는 방법을 알아봅니다.

[리소스 배포 순서 설정](./resource-manager-tutorial-create-templates-with-dependent-resources.md) 자습서에서는 가상 머신, 가상 네트워크 및 저장소 계정을 포함한 기타 종속 리소스를 만듭니다. 매번 새 저장소 계정을 만드는 대신, 사용자가 새 저장소 계정을 만들지, 기존 저장소 계정을 사용할지 선택하도록 할 수 있습니다. 이 목표를 달성하기 위해 추가 매개 변수를 정의합니다. 매개 변수의 값이 “new”인 경우 새 저장소 계정이 만들어집니다. 그렇지 않을 경우 지정된 이름의 기존 스토리지 계정이 사용됩니다.

![Resource Manager 템플릿 사용 조건 다이어그램](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 수정
> * 템플릿 배포
> * 리소스 정리

이 자습서에서는 기본적인 조건 사용 시나리오만 다룹니다. 자세한 내용은 다음을 참조하세요.

* [템플릿 파일 구조: 조건](./resource-group-authoring-templates.md#condition).
* [조건부로 Azure Resource Manager 템플릿의 리소스를 배포합니다](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [템플릿 함수: If](./resource-group-template-functions-logical.md#if).
* [Azure Resource Manager 템플릿용 비교 함수](./resource-group-template-functions-comparison.md)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. **열기**를 선택하여 파일을 엽니다.
4. 템플릿에 5개 리소스가 정의되어 있습니다.

   * `Microsoft.Storage/storageAccounts`. [템플릿 참조](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)를 참조하세요.
   * `Microsoft.Network/publicIPAddresses`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)를 참조하세요.
   * `Microsoft.Network/virtualNetworks`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)를 참조하세요.
   * `Microsoft.Network/networkInterfaces`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)를 참조하세요.
   * `Microsoft.Compute/virtualMachines`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.

     템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
5. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.

## <a name="modify-the-template"></a>템플릿 수정

기존 템플릿에서 두 가지를 변경합니다.

* 저장소 계정 이름 매개 변수를 추가합니다. 사용자는 새 저장소 계정 이름 또는 기존 저장소 계정 이름을 지정할 수 있습니다.
* **newOrExisting**이라는 새 매개 변수를 추가합니다. 배포에서는 이 매개 변수를 사용하여 새 저장소 계정을 만들 위치를 결정하거나 기존 저장소 계정을 사용합니다.

변경 절차는 다음과 같습니다.

1. Visual Studio Code에서 **azuredeploy.json**을 엽니다.
2. 템플릿 전체에서 3개의 **variables('storageAccountName')** 를 **parameters('storageAccountName')** 로 바꿉니다.
3. 다음 변수 정의를 제거합니다.

    ![Resource Manager 템플릿 사용 조건 다이어그램](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. 다음 두 매개 변수를 템플릿에 추가합니다.

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    업데이트된 매개 변수 정의는 다음과 같습니다.

    ![Resource Manager 사용 조건](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. 저장소 계정 정의의 시작 부분에 다음 줄을 추가합니다.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    조건은 **newOrExisting**이라는 매개 변수의 값을 확인합니다. 매개 변수 값이 **new**이면 배포에서 저장소 계정을 만듭니다.

    업데이트된 저장소 계정 정의는 다음과 같습니다.

    ![Resource Manager 사용 조건](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. 가상 머신 리소스 정의의 **storageUri** 속성을 다음 값으로 업데이트합니다.

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    다른 리소스 그룹에서 기존 저장소 계정을 사용하는 경우 이 변경은 필요합니다.

7. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

[템플릿 배포](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)의 지침을 따라 Cloud Shell을 열고 수정된 템플릿을 업로드한 후 다음 PowerShell 스크립트를 실행하여 템플릿을 배포합니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> **newOrExisting**이 **new**이지만 지정된 저장소 계정 이름의 저장소 계정이 이미 있는 경우 배포가 실패합니다.

**newOrExisting**이 “existing”으로 설정된 다른 배포를 시도하고 기존 저장 계정을 지정해 보세요. 저장소 계정을 미리 들려면 [저장소 계정 만들기](../storage/common/storage-quickstart-create-account.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. 리소스 그룹을 삭제하려면 **사용해 보기**를 선택하여 Cloud Shell을 엽니다. PowerShell 스크립트를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기**를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새로운 저장소 계정을 만들지, 기존 저장소 계정을 사용할지 사용자가 선택할 수 있는 템플릿을 개발했습니다. Azure Key Vault에서 비밀을 검색하고 해당 비밀을 템플릿 배포에 암호로 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [템플릿 배포에서 Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)
