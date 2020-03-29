---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 Azure 디스크 암호화에 사용할 키 자격 증명 모음을 만들고 구성하는 단계를 제공합니다.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970637"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성

Azure 디스크 암호화는 Azure Key Vault를 사용하여 디스크 암호화 키 및 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/key-vault-get-started.md) 및 [키 자격 증명 모음 보안](../../key-vault/key-vault-secure-your-key-vault.md)을 참조하세요. 

> [!WARNING]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 사용한 Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault-aad.md) 참조하십시오.

Azure 디스크 암호화에 사용할 키 자격 증명 모음을 만들고 구성하려면 다음 세 단계가 포함됩니다.

1. 필요한 경우 리소스 그룹을 만듭니다.
2. 키 자격 증명 모음 만들기. 
3. 키 볼트 고급 액세스 정책 설정

이러한 단계는 다음 빠른 시작에 나와 있습니다.

- [Azure CLI를 사용하여 Linux VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [Azure PowerShell을 사용하여 Linux VM 생성 및 암호화](disk-encryption-cli-quickstart.md)

원하는 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

> [!Note]
> 이 문서의 단계는 [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 Azure 디스크 암호화 필수 구성 조건 [PowerShell 스크립트에서](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)자동화됩니다.

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치하고 Azure에 연결

이 문서의 단계는 Azure [CLI, Azure](/cli/azure/) [PowerShell Az 모듈](/powershell/azure/overview)또는 Azure [포털을](https://portal.azure.com)통해 완료할 수 있습니다. 

브라우저를 통해 포털에 액세스할 수 있지만 Azure CLI 및 Azure PowerShell은 로컬 설치가 필요합니다. [리눅스에 대 한 Azure 디스크 암호화 를 참조:](disk-encryption-linux.md#install-tools-and-connect-to-azure) 자세한 내용은 도구를 설치 합니다.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell을 사용하기 전에 먼저 Azure 구독에 연결해야 합니다. 이렇게 하려면 [Azure CLI로 로그인,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [Azure Powershell으로 로그인](/powershell/azure/authenticate-azureps?view=azps-2.5.0)또는 메시지가 표시되면 Azure 포털에 자격 증명을 제공합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

*리소스 그룹이 이미 있는 경우 키 [자격 증명 모음 만들기를](#create-a-key-vault)건너뛸 수 있습니다.*

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

az 그룹을 사용하여 리소스 그룹을 [만들면](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI 명령, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 명령 또는 [Azure 포털에서](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

*키 자격 증명 모음이 이미 있는 경우 [키 자격 증명 모음 고급 액세스 정책 으로](#set-key-vault-advanced-access-policies)건너뛸 수 있습니다.*

az 키 볼트 [를](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 사용하여 키 볼트를 만들 Azure CLI 명령, [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 명령, [Azure 포털](https://portal.azure.com)또는 리소스 [관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)만듭니다.

>[!WARNING]
> 암호화 암호가 지역 경계를 넘지 않도록 하려면 Azure 디스크 암호화를 위해 키 자격 증명 모음과 VM이 동일한 지역에 공동 으로 배치되어야 합니다. 암호화할 VM과 동일한 지역에 있는 키 자격 증명 모음을 만들고 사용합니다. 

각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 <your-unique-keyvault-name>을 Key Vault의 이름으로 바꿉니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 키 자격 증명 모음을 만들 때 "--디스크암호화 가능" 플래그를 추가합니다.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 키 자격 증명 모음을 만들 때 "-EnabledForDiskEncryption" 플래그를 추가합니다.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 템플릿

[리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)사용하여 키 자격 증명 모음을 만들 수도 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.
2. 구독, 리소스 그룹, 리소스 그룹 위치, 키 볼트 이름, 개체 ID, 법적 용어 및 계약을 선택한 다음 **구입을**클릭합니다. 


##  <a name="set-key-vault-advanced-access-policies"></a>Key Vault 고급 액세스 정책 설정

Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 Key Vault의 암호화 키 또는 비밀에 액세스해야 합니다. 

이전 단계에서 설명한 것처럼 디스크 암호화, 배포 또는 템플릿 배포에 대한 키 자격 증명 모음을 사용하도록 설정하지 않은 경우 고급 액세스 정책을 업데이트해야 합니다.  

### <a name="azure-cli"></a>Azure CLI

[az keyvault update](/cli/azure/keyvault#az-keyvault-update)를 사용하여 키 자격 증명 모음에 대한 디스크 암호화를 사용하도록 설정합니다. 

 - **디스크 암호화에 Key Vault 사용:** Enabled-for-disk-encryption이 필요합니다. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **필요한 경우 템플릿 배포에 Key Vault 사용:** Resource Manager에서 자격 증명 모음으로부터 비밀을 검색할 수 있도록 허용합니다.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 키 볼트 PowerShell cmdlet [Set-AzKeyVaultAccessPolicy를](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 사용하여 키 자격 증명 모음에 대한 디스크 암호화를 사용하도록 설정합니다.

  - **디스크 암호화에 Key Vault 사용:** Azure Disk Encryption에 EnabledForDiskEncryption이 필요합니다.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **필요한 경우 템플릿 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 템플릿 배포에서 참조되는 경우 Azure Resource Manager에서 이 키 자격 증명 모음으로부터 비밀을 가져올 수 있도록 합니다.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure portal

1. 키 자격 증명 모음을 선택하고 **정책 으로**이동한 다음 **클릭하여 고급 액세스 정책을 표시합니다.**
2. **볼륨 암호화를 위해 Azure Disk Encryption에 대한 액세스 사용**이라는 레이블이 있는 상자를 선택합니다.
3. 필요한 경우 **배포를 위해 Azure Virtual Machines에 대한 액세스 사용** 및/또는 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 
4. **저장**을 클릭합니다.

    ![Azure 키 자격 증명 모음에 대한 고급 액세스 정책](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>키 암호화 키 설정(KEK)

암호화 키에 대한 추가 보안 계층에 KEK(키 암호화 키)를 사용하려면 키 자격 증명 모음에 KEK를 추가합니다. 키 암호화 키가 지정되면 Azure Disk Encryption에서 해당 키를 사용하여 Key Vault에 쓰기 전에 암호화 비밀을 래핑합니다.

Azure CLI [az 키볼트 만들기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령, Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 또는 Azure [포털을](https://portal.azure.com/)사용하여 새 KEK를 생성할 수 있습니다. RSA 키 유형을 생성해야 합니다. Azure 디스크 암호화는 아직 타원 곡선 키를 사용하여 지원하지 않습니다.

대신 온-프레미스 키 관리 HSM에서 KEK를 가져올 수 있습니다. 자세한 내용은 [키 자격 증명 모음 문서를](../../key-vault/key-vault-hsm-protected-keys.md)참조하십시오. 

키 볼트 KEK URL의 버전이 있어야 합니다. Azure에서 이 버전 관리 제한을 적용합니다. 유효한 비밀과 KEK URL은 다음 예제를 참조하세요.

* 유효한 비밀 URL의 예:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 유효한 KEK URL의 예:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption은 키 자격 증명 모음 비밀 및 KEK URL의 일부로 포트 번호 지정을 지원하지 않습니다. 지원되거나 지원되지 않는 키 자격 증명 모음 URL에 대한 예제는 다음 예제를 참조하세요.

  * 허용 가능한 키 볼트 URL:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 허용되지 않는 키 볼트 URL:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Azure CLI [az 키볼트 만들기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용하여 새 KEK를 생성하고 키 자격 증명 모음에 저장합니다.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. 대신 Azure CLI [az 키볼트 키 볼트 키 가져오기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용하여 개인 키를 가져올 수 있습니다.

두 경우 모두 KEK 이름을 Azure CLI az [vm 암호화 활성화--키](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) 암호화 키 매개 변수에 제공합니다. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet을 사용하여 새 KEK를 생성하고 키 자격 증명 모음에 저장합니다.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

대신 Azure PowerShell [az 키볼트 키 볼트 키 가져오기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용하여 개인 키를 가져올 수 있습니다.

두 경우 모두 KEK 키 볼트의 ID와 KEK의 URL을 Azure PowerShell [Set-AzVMDisk암호화확장-KeyEncryptionKeyVaultId](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) 및 -KeyEncryptionKeyUrl 매개 변수에 제공합니다. 이 예제에서는 디스크 암호화 키와 KEK 모두에 대해 동일한 키 자격 증명 모음을 사용하고 있다고 가정합니다.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>다음 단계

- [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 디스크 암호화 필수 구성 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Linux VM에서 Azure 디스크 암호화 시나리오](disk-encryption-linux.md) 알아보기
- [Azure 디스크 암호화 문제 해결](disk-encryption-troubleshooting.md) 방법 알아보기
- Azure [디스크 암호화 샘플 스크립트](disk-encryption-sample-scripts.md) 읽기
