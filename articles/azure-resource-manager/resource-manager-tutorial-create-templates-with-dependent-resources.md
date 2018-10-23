---
title: 종속 리소스를 사용하여 Azure Resource Manager 템플릿 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 여러 리소스가 포함된 Azure Resource Manager 템플릿을 만들고 배포하는 방법을 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114315"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기

여러 리소스를 배포하는 Azure Resource Manager 템플릿을 만드는 방법을 알아봅니다.  템플릿을 만든 후에는 Azure Portal에서 Cloud shell을 사용하여 템플릿을 배포합니다.

이 자습서에서는 저장소 계정, 가상 머신, 가상 네트워크 및 몇 가지 다른 종속 리소스를 만듭니다. 일부 리소스는 다른 리소스가 존재하기 전에는 배포할 수 없습니다. 예를 들어 저장소 계정 및 네트워크 인터페이스가 없으면 가상 머신을 만들 수 없습니다. 한 리소스를 다른 리소스의 종속 리소스로 만들어서 이 관계를 정의할 수 있습니다. Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 자세한 내용은 [Azure Resource Manager 템플릿에서 리소스를 배포하는 순서 정의](./resource-group-define-dependencies.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Key Vault 준비
> * 빠른 시작 템플릿 열기
> * 템플릿 탐색
> * 매개 변수 파일 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager Tools 확장이 있는 [Visual Studio Code](https://code.visualstudio.com/)   [확장 설치](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)를 참조하세요.

## <a name="prepare-key-vault"></a>Key Vault 준비

암호 스프레이 공격을 방지하기 위해 가상 머신 관리자 계정에는 자동 생성 암호를 사용하고 Key Vault를 통해 암호를 저장하는 것이 좋습니다. 다음 절차에서는 Key Vault 및 비밀을 만들어 암호를 저장합니다. 또한 템플릿 배포에 필요한 권한을 구성하여 Key Vault에 저장된 비밀에 액세스합니다. 다른 Azure 구독에 Key Vault가 있는 경우 추가적인 액세스 정책이 필요합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](./resource-manager-keyvault-parameter.md)을 참조하세요.

1. [Azure Cloud Shell](https://shell.azure.com)에 로그인합니다.
2. 왼쪽 위 모서리에서 **PowerShell** 또는 **Bash** 등, 원하는 환경으로 전환합니다.
3. 다음 Azure PowerShell 또는 Azure CLI 명령을 실행합니다.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. 출력 값을 기록합니다. 자습서의 뒷부분에서 필요합니다.

> [!NOTE]
> Azure 서비스마다 특정한 암호 요구 사항이 있습니다. 예를 들어 Azure 가상 머신 요구 사항은 VM을 만들 때 암호 요구 사항에서 확인할 수 있습니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.
5. 1~4단계를 반복하여 **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json**을 연 다음, 파일을 **azuredeploy.parameters.json**으로 저장합니다.

## <a name="explore-the-template"></a>템플릿 탐색

이 섹션의 템플릿을 탐색하는 경우 다음 질문에 대답해 보세요.

- 이 템플릿에 정의되는 Azure 리소스는 얼마나 되나요?
- 리소스 중 하나는 Azure 저장소 계정입니다.  마지막 자습서에서 사용한 것과 비슷한 같은 정의인가요?
- 이 템플릿에 정의된 리소스에 대한 템플릿 참조를 찾을 수 있나요?
- 리소스의 종속성을 찾을 수 있나요?

1. Visual Studio Code에서 **리소스** 내부에 첫 번째 수준 요소와 두 번째 수준 요소만 표시될 때까지 요소를 축소합니다.

    ![Visual Studio Code Azure Resource Manager 템플릿](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    템플릿에 5개 리소스가 정의되어 있습니다.
2. 첫 번째 리소스를 확장합니다. 저장소 계정입니다. 정의는 마지막 자습서의 시작 부분에서 사용한 것과 동일해야 합니다.

    ![Visual Studio Code Azure Resource Manager 템플릿 - 저장소 계정 정의](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 두 번째 리소스를 확장합니다. 리소스 종류는 **Microsoft.Network/publicIPAddresses**입니다. 템플릿 참조를 찾으려면 [템플릿 참조](https://docs.microsoft.com/azure/templates/)로 이동하고, **제목으로 필터링** 필드에서 **public ip address** 또는 **public ip addresses**를 입력합니다. 리소스 정의를 템플릿 참조와 비교합니다.

    ![Visual Studio Code Azure Resource Manager 템플릿 - 공용 IP 주소 정의](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 이 템플릿에 정의된 다른 리소스에 대한 템플릿 참조를 찾으려면 마지막 단계를 반복합니다.  리소스 정의와 참조를 비교합니다.
5. 네 번째 리소스를 확장합니다.

    ![Visual Studio Code Azure Resource Manager 템플릿 dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 요소를 사용하면 한 리소스를 하나 이상의 리소스에 종속된 것으로 정의할 수 있습니다. 이 예에서 이 리소스는 networkInterface입니다.  이 리소스는 다음과 같은 두 리소스에 종속됩니다.

    * publicIPAddress
    * virtualNetwork

6. 다섯 번째 리소스를 확장합니다. 이 리소스는 가상 머신입니다. 이 리소스는 다음과 같은 두 리소스에 종속됩니다.

    * storageAccount
    * networkInterface

다음 다이어그램은 이 템플릿의 리소스 및 종속성 정보를 보여줍니다.

![Visual Studio Code Azure Resource Manager 템플릿 종속성 다이어그램](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

종속성을 지정하면 Resource Manager가 솔루션을 효율적으로 배포합니다. 종속성이 없기 때문에 저장소 계정, 공용 IP 주소 및 가상 네트워크가 병렬로 배포됩니다. 공용 IP 주소 및 가상 네트워크가 배포된 후에는 네트워크 인터페이스가 생성됩니다. 다른 모든 리소스가 배포되면 Resource Manager는 가상 머신을 배포합니다.

## <a name="edit-the-parameters-file"></a>매개 변수 파일 편집

템플릿 파일은 변경하지 않아도 됩니다. 하지만 관리자 암호를 Key Vault에서 검색하도록 매개 변수 파일을 수정해야 합니다.

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
    **id**를 마지막 절차에서 만든 Key Vault의 리소스 ID로 바꿉니다. 출력 중 하나입니다. 

    ![키 자격 증명 모음과 Resource Manager 템플릿 가상 머신 배포 매개 변수 파일 통합](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 다음 항목에 대한 값을 제공합니다.

    - **adminUsername**: 가상 머신 관리자 계정의 이름을 지정합니다.
    - **dnsLabelPrefix**: dnsLablePrefix의 이름을 지정합니다.
4. 변경 내용을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하는 방법에는 여러 가지가 있습니다.  이 자습서에서는 Azure Portal에서 Cloud Shell을 사용합니다.

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다. [Azure Portal](https://portal.azure.com)에서 로그인한 다음, 아래 이미지와 같이 오른쪽 위 모서리에서 **Cloud Shell**을 선택할 수도 있습니다.

    ![Azure Portal - Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Cloud shell의 왼쪽 위 모서리에서 **PowerShell**을 선택한 다음, **확인**을 선택합니다.  이 자습서에서는 PowerShell을 사용합니다.
3. Cloud shell에서 **파일 업로드**를 선택합니다.

    ![Azure Portal - Cloud Shell 파일 업로드](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. 자습서의 앞부분에서 저장한 파일을 선택합니다. 기본 이름은 **azuredeploy.json** 및 **azuredeploy.paraemters.json**입니다.  이름이 같은 파일이 있는 경우 알림 없이 기존 파일을 덮어씁니다.
5. Cloud shell에서 다음 명령을 실행하여 파일이 성공적으로 업로드되었는지 확인합니다. 

    ```bash
    ls
    ```

    ![Azure Portal - Cloud Shell 파일 나열](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    스크린샷에 표시된 파일 이름은 azuredeploy.json입니다.

6. Cloud shell에서 다음 명령을 실행하여 JSON 파일의 콘텐츠를 확인합니다.

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Cloud shell에서 다음 PowerShell 명령을 실행합니다. 샘플 스크립트는 Key Vault에 대해 만든 동일한 리소스 그룹을 사용합니다. 동일한 리소스 그룹을 사용하면 리소스 정리가 더 쉽습니다.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. 다음 PowerShell 명령을 실행하여 새로 만든 가상 머신을 나열합니다.

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    가상 머신 이름은 템플릿 내에서 **SimpleWinVM**으로 하드 코딩됩니다.

9. 관리자의 자격 증명을 테스트하기 위해 가상 머신에 로그인합니다. 

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 머신, 가상 네트워크 및 종속 리소스를 만드는 템플릿을 개발하고 배포합니다. 조건에 따라 Azure 리소스를 배포하는 방법을 배우려면 다음을 참조하세요.


> [!div class="nextstepaction"]
> [조건 사용](./resource-manager-tutorial-use-conditions.md)

