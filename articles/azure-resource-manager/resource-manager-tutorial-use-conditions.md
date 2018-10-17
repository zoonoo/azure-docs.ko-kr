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
ms.date: 10/02/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 216e474f519e57352b017dc3e6bcdd74d48b03de
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238649"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>자습서: Azure Resource Manager 템플릿에서 조건 사용

조건에 따라 Azure 리소스를 배포하는 방법을 알아봅니다. 

이 자습서에 사용된 시나리오는 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)에 사용된 것과 비슷합니다. 이 자습서에서는 저장소 계정, 가상 머신, 가상 네트워크 등 몇 가지 다른 종속 리소스를 만듭니다. 새 저장소 계정을 만드는 대신, 사용자가 새 저장소 계정을 만들지, 기존 저장소 계정을 사용할지 선택하도록 할 수 있습니다. 이 목표를 달성하기 위해 추가 매개 변수를 정의합니다. 매개 변수의 값이 “new”인 경우 새 저장소 계정이 만들어집니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 수정
> * 템플릿 배포
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.

## <a name="modify-the-template"></a>템플릿 수정

기존 템플릿에서 두 가지를 변경합니다.

* 저장소 계정 이름을 제공하는 데 사용되는 매개 변수를 추가합니다. 이 매개 변수는 사용자에게 기존 저장소 계정 이름을 지정하는 옵션을 제공합니다. 또한 새 저장소 계정 이름으로 사용할 수도 있습니다.
* **newOrExisting**이라는 새 매개 변수를 추가합니다. 배포에서는 이 매개 변수를 사용하여 새 저장소 계정을 만들 위치를 결정하거나 기존 저장소 계정을 사용합니다.

1. Visual Studio Code에서 **azuredeploy.json**을 엽니다.
2. 템플릿 전체에서 **variables('storageAccountName')** 를 **parameters('storageAccountName')** 로 바꿉니다.  **variables('storageAccountName')** 에는 세 가지 모양이 있습니다.
3. 다음 변수 정의를 제거합니다.

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. 다음 두 매개 변수를 템플릿에 추가합니다.

    ```json
    "newOrExisting": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    ```
    업데이트된 매개 변수 정의는 다음과 같습니다.

    ![Resource Manager 사용 조건](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. 저장소 계정 정의의 시작 부분에 다음 줄을 추가합니다.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'yes')]",
    ```

    조건은 **newOrExisting**이라는 매개 변수의 값을 확인합니다. 매개 변수 값이 **new**이면 배포에서 저장소 계정을 만듭니다.

    업데이트된 저장소 계정 정의는 다음과 같습니다.

    ![Resource Manager 사용 조건](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)

6. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

[템플릿 배포](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)의 지침에 따라 템플릿을 배포합니다.

Azure PowerShell을 사용하여 템플릿을 배포할 때 추가 매개 변수를 하나 지정해야 합니다.

```powershell
$resourceGroupName = "<Enter the resource group name>"
$storageAccountName = "Enter the storage account name>"
$location = "<Enter the Azure location>"
$vmAdmin = "<Enter the admin username>"
$vmPassword = "<Enter the password>"
$dnsLabelPrefix = "<Enter the prefix>"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting "new"
```

> [!NOTE]
> **newOrExisting**이 **new**이지만 지정된 저장소 계정 이름의 저장소 계정이 이미 있는 경우 배포가 실패합니다.

**newOrExisting**이 “existing”으로 설정된 다른 배포를 시도하고 기존 저장 계정을 지정해 보세요. 저장소 계정을 미리 들려면 [저장소 계정 만들기](../storage/common/storage-quickstart-create-account.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새로운 저장소 계정을 만들지, 기존 저장소 계정을 사용할지 사용자가 선택할 수 있도록 하는 템플릿을 개발합니다. 이 자습서에서 만든 가상 머신에는 관리자 사용자 이름 및 암호가 필요합니다. 배포 중에 암호를 전달하는 대신 Azure Key Vault를 사용하여 암호를 미리 저장하고 배포 중에 암호를 검색할 수 있습니다. Azure Key Vault에서 비밀을 검색하고 템플릿 배포에서 비밀을 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [템플릿 배포에서 Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)
