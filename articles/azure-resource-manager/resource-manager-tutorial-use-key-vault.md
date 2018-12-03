---
title: Resource Manager 템플릿 배포 시 Azure Key Vault 통합 | Microsoft Docs
description: Azure Key Vault를 사용하여 Resource Manager 템플릿 배포 중에 보안 매개 변수 값을 전달하는 방법을 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0bcff6e0cec234f17b0aaab9828602eb4a194d85
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334213"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합

Azure Key Vault에서 비밀 값을 검색하여 Resource Manager 배포 중에 비밀 값을 매개 변수로 전달하는 방법을 알아봅니다. 이 값은 해당 Key Vault ID만 참조되기 때문에 절대 노출되지 않습니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](./resource-manager-keyvault-parameter.md)을 참조하세요.

[리소스 배포 순서 설정](./resource-manager-tutorial-create-templates-with-dependent-resources.md) 자습서에서는 가상 머신, 가상 네트워크 및 기타 종속 리소스를 만듭니다. 이 자습서에서는 Azure Key Vault에서 가상 머신 관리자 암호를 검색하도록 템플릿을 사용자 지정합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Key Vault 준비
> * 빠른 시작 템플릿 열기
> * 매개 변수 파일 편집
> * 템플릿 배포
> * 배포 유효성 검사
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-the-key-vault"></a>Key Vault 준비

이 섹션에서는 Resource Manager 템플릿을 사용하여 Key Vault 및 비밀을 만듭니다. 이 템플릿은:

* `enabledForTemplateDeployment` 속성이 활성화된 Key Vault를 만듭니다. 템플릿 배포 프로세스가 이 Key Vault에 정의된 비밀에 액세스할 수 있으려면 이 속성이 true여야 합니다.
* Key Vault에 비밀을 추가합니다.  비밀에는 가상 머신 관리자 암호가 저장됩니다.

가상 머신 템플릿을 배포하는 사용자가 Key Vault의 소유자나 기여자가 아닌 경우에는, Key Vault의 소유자나 기여자가 Key Vault에 대한 Microsoft.KeyVault/vaults/deploy/action 권한에 대한 액세스를 해당 사용자에게 반드시 부여해야 합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](./resource-manager-keyvault-parameter.md)을 참조하세요.

권한을 구성하려면 템플릿에 Azure AD 사용자 개체 ID가 필요합니다. 다음 절차에서는 개체 ID(GUID)를 가져오고 관리자 암호도 생성합니다. 암호 스프레이 공격을 방지하려면 생성된 암호를 사용하는 것이 좋습니다.

1. 다음 Azure PowerShell 또는 Azure CLI 명령을 실행합니다.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. 개체 ID와 생성된 암호를 모두 적어둡니다. 나중에 필요합니다.
3. 생성된 암호가 가상 머신 암호 요구 사항을 충족하는지 확인합니다. Azure 서비스마다 특정한 암호 요구 사항이 있습니다. VM 암호 요구 사항은 [VM을 만들 때의 암호 요구 사항은 무엇인가요?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)를 참조하세요.

Key Vault를 만들려면:

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 Key Vault 및 Key Vault 비밀이 생성됩니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" /></a>

2. 다음 값을 선택하거나 입력합니다.  값을 입력한 후에 **구매**를 선택하지 마십시오.

    ![Resource Manager 템플릿 Key Vault 통합 배포 포털](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: 고유 이름을 지정합니다. 이 이름을 적어두십시오. 동일한 리소스 그룹을 사용하여 다음 세션에서 가상 머신을 배포하게 됩니다. 동일한 리소스 그룹에 Key Vault와 가상 머신을 둘 다 배치하면 자습서의 끝 부분에서 리소스를 정리하기 쉽습니다.
    * **위치**: 위치를 선택합니다.  기본 위치는 **미국 중부**입니다.
    * **Key Vault 이름**: 고유한 이름을 지정합니다. 
    * **테넌트 ID**: 템플릿 함수가 자동으로 검색한 테넌트 ID입니다.  기본값을 변경하지 마십시오.
    * **AD 사용자 ID**: 마지막 절차에서 검색한 Azure AD 사용자 개체 ID를 입력합니다.
    * **비밀 이름**: 기본 이름은 **vmAdminPassword**입니다. 여기에서 비밀 이름을 변경하면 가상 머신을 배포할 때 비밀 이름을 업데이트해야 합니다.
    * **비밀 값**: 비밀을 입력합니다.  비밀은 가상 머신에 로그인할 때 사용되는 암호입니다. 마지막 절차에서 만든 생성된 암호를 사용하는 것이 좋습니다.
    * **위에 명시된 사용 약관에 동의함**: 선택합니다.
3. 맨 위에서 **매개 변수 편집**을 선택하여 템플릿을 살펴봅니다.
4. 템플릿 JSON 파일의 28번 줄로 이동합니다. 이것이 Key Vault 리소스 정의입니다.
5. 35번 줄로 이동합니다.

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment`는 Key Vault 속성입니다. 배포하는 동안 이 Key Vault에서 비밀을 검색할 수 있으려면 이 속성이 true여야 합니다.
6. 89번 줄로 이동합니다. Key Vault 비밀 정의입니다.
7. 페이지 아래쪽에서 **삭제**를 선택합니다. 변경은 하지 않습니다.
8. 이전 스크린샷과 같이 모든 값을 입력했는지 확인한 다음, 페이지 맨 아래에서 **구매**를 클릭합니다.
9. 페이지 상단에서 벨 아이콘(알림)을 선택하여 **알림** 창을 엽니다. 리소스 배포가 완료될 때까지 기다립니다.
10. **알림** 창에서 **리소스 그룹으로 이동**을 선택합니다. 
11. Key Vault 이름을 선택하여 엽니다.
12. 왼쪽 창에서 **액세스 정책**을 선택합니다. 내 이름(Active Directory)이 나열되어야 하며, 그렇지 않으면 키 저장소에 액세스할 수 있는 권한이 없습니다.
13. **클릭하여 고급 액세스 정책 표시**를 선택합니다. **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**이 선택되어 있는지 확인합니다. 이것은 Key Vault 통합이 작동하기 위한 또 다른 조건입니다.

    ![Resource Manager 템플릿 Key Vault 통합 액세스 정책](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. 왼쪽 창에서 **속성**을 선택합니다.
15. **리소스 ID**의 복사본을 만들어 둡니다. 이 ID는 가상 머신을 배포할 때 필요합니다.  리소스 ID 형식은 다음가 같습니다.

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다. [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)에 사용된 시나리오와 동일한 시나리오입니다.
4. 템플릿에 5개 리소스가 정의되어 있습니다.

    * `Microsoft.Storage/storageAccounts` [템플릿 참조](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)를 참조하세요.
    * `Microsoft.Network/publicIPAddresses` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)를 참조하세요.
    * `Microsoft.Network/virtualNetworks` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)를 참조하세요.
    * `Microsoft.Network/networkInterfaces` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)를 참조하세요.
    * `Microsoft.Compute/virtualMachines` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.

    템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
5. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.
6. 1~4단계를 반복하여 다음 URL을 연 다음 파일을 **azuredeploy.parameters.json**으로 저장합니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>매개 변수 파일 편집

템플릿 파일은 변경하지 않아도 됩니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 **azuredeploy.parameters.json**을 엽니다.
2. **adminPassword** 매개 변수를 다음으로 업데이트합니다.

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    **id**를 마지막 절차에서 만든 Key Vault의 리소스 ID로 바꿉니다.  

    ![키 자격 증명 모음과 Resource Manager 템플릿 가상 머신 배포 매개 변수 파일 통합](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 다음 항목에 대한 값을 제공합니다.

    * **adminUsername**: 가상 머신 관리자 계정의 이름을 지정합니다.
    * **dnsLabelPrefix**: dnsLabelPrefix의 이름을 지정합니다.
4. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

[템플릿 배포](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)의 지침에 따라 템플릿을 배포합니다. **azuredeploy.json**과 **azuredeploy.parameters.json**을 둘 다 Cloud Shell에 업로드한 후, 다음 PowerShell 스크립트를 사용하여 템플릿을 배포합니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

템플릿을 배포하는 경우 Key Vault와 동일한 리소스 그룹을 사용합니다. 그러면 리소스 그룹을 쉽게 정리할 수 있습니다. 리소스 그룹을 두 개가 아닌 하나만 삭제하면 됩니다.

## <a name="valid-the-deployment"></a>배포 유효성 검사

가상 머신 배포에 성공한 후에는 Key Vault에 저장된 암호를 사용하여 로그인을 테스트합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. **리소스 그룹**/**YourResourceGroupName>**/**simpleWinVM**을 선택합니다.
3. 위에서 **연결**을 선택합니다.
4. **RDP 파일 다운로드**를 선택한 다음, 지침에 따라 Key Vault에 저장된 암호를 사용하여 가상 머신에 로그인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Key Vault에서 비밀을 검색하여 템플릿 배포에 이 비밀을 사용했습니다.  연결된 템플릿을 만드는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [연결된 템플릿 만들기](./resource-manager-tutorial-create-linked-templates.md)
