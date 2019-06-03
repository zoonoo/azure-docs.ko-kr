---
title: 필수 구성 요소 - IaaS VM용 Azure Disk Encryption | Microsoft Docs
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption을 사용하기 위한 필수 구성 요소를 설명합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 6874258c31d4dd7d2a0aa0042624ee57616c0a89
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234277"
---
# <a name="azure-disk-encryption-prerequisites"></a>Azure Disk Encryption 필수 구성 요소

'Azure Disk Encryption 필수 구성 요소'라는 이 문서에서는 Azure Disk Encryption을 사용하기 전에 필요한 항목에 대해 설명합니다. Azure Disk Encryption은 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)와 통합되어 암호화 키를 관리하는 데 도움이 됩니다. [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Disk Encryption을 구성할 수 있습니다.

[Azure Disk Encryption 개요](azure-security-disk-encryption-overview.md) 문서에서 설명한 지원되는 시나리오에 대해 Azure IaaS VM에서 Azure Disk Encryption을 사용하도록 설정하려면 먼저 필수 구성 요소가 준비되어 있어야 합니다. 

> [!WARNING]
> - 이전에 [Azure AD 앱에서 Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md)를 사용하여 이 VM을 암호화한 경우에는 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 이는 지원되는 시나리오가 아니므로 이 암호화된 VM에서는 [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)을 사용할 수 없습니다. 즉, 이 암호화된 VM을 위해 AAD 애플리케이션에서 전환하는 기능은 아직 지원되지 않습니다.
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> 지원되는 운영 체제
Azure Disk Encryption이 지원되는 운영 체제는 다음과 같습니다.

- Windows Server 버전: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, Windows Server 2012 R2 Server Core 및 Windows Server 2016 Server core.
Windows Server 2008 R2의 경우 Azure에서 암호화를 사용하도록 설정하기 전에 .NET Framework 4.5를 설치해야 합니다. Windows Server 2008 R2 x64 기반 시스템 (KB2901983) 용 Microsoft.NET Framework 4.5.2 선택적 업데이트를 사용 하 여 Windows 업데이트에서 설치 하세요.
- Windows Server 2012 R2 Core 및 Windows Server 2016 Core bdehdcfg 구성 요소는 VM에 설치 되 면 Azure Disk Encryption에서 지원 됩니다.
- Windows 클라이언트 버전: Windows 8 클라이언트 및 Windows 10 클라이언트
- Azure Disk Encryption은 특정 Azure 갤러리 기반 Linux 서버 배포판 및 버전에서 지원됩니다. 현재 지원되는 버전 목록은 [Azure Disk Encryption FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)를 참조하세요. 참조를 [azure 보증 Linux 배포판](../virtual-machines/linux/endorsed-distros.md) 하 고 Microsoft에서 지원 되는 이미지 목록에 대 한 합니다 [지원 Azure Disk Encryption은 어떤 Linux 배포판?](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 에 [Azure 디스크 암호화 FAQ](azure-security-disk-encryption-faq.md) 보증된 이미지 배포에 따라 현재 지원 되는 버전의 목록은 합니다.
- Azure Disk Encryption은 Key Vault 및 VM이 동일한 Azure 하위 지역 및 구독에 있어야 합니다. 별도 하위 지역에서 리소스를 구성하면 Azure Disk Encryption 기능 사용 시 오류가 발생합니다.

## <a name="bkmk_LinuxPrereq"></a> Linux IaaS VM에 대한 추가 필수 구성 요소 

- Linux용 Azure Disk Encryption에는 [지원되는 이미지](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)에서 OS 디스크 암호화를 사용하도록 설정하기 위해 VM에 7GB의 RAM이 필요합니다. OS 디스크 암호화 프로세스가 완료되면 VM을 적은 메모리로 실행하도록 구성할 수 있습니다.
- Azure Disk Encryption에서는 vfat 모듈 시스템에 있어야 합니다.  제거 하거나 기본 이미지에서이 모듈을 사용 하지 않도록 설정 하면 시스템 키 볼륨을 읽고 후속 다시 부팅 후에 디스크를 잠금 해제 하는 데 필요한 키를 가져올 수 없습니다. 시스템에서 vfat 모듈을 제거 하는 시스템 강화 단계 Azure Disk Encryption을 사용 하 여 호환 되지 않습니다. 
- 암호화를 사용하도록 설정하기 전에 암호화할 데이터 디스크를 /etc/fstab에 올바르게 나열해야 합니다. "/dev/sdX" 형식의 디바이스 이름은 특히 암호화가 적용된 후 다시 부팅할 때 동일한 디스크에 연결될 수 없으므로 이 항목에 영구 블록 디바이스 이름을 사용합니다. 이 동작에 대한 자세한 내용은 [Linux VM 디바이스 이름 변경 문제 해결](../virtual-machines/linux/troubleshoot-device-names-problems.md)을 참조하세요.
- /etc/fstab 설정이 탑재에 대해 올바르게 구성되었는지 확인합니다. 이러한 설정을 구성하려면 mount -a 명령을 실행하거나 VM을 다시 부팅하고 다시 탑재를 트리거합니다. 이러한 작업이 완료되면 lsblk 명령의 출력을 확인하여 드라이브가 여전히 탑재되어 있는지 확인합니다. 
  - 암호화를 사용하도록 설정하기 전에 /etc/fstab 파일에서 드라이브를 올바르게 탑재하지 않으면 Azure Disk Encryption에서 해당 드라이브를 올바르게 탑재할 수 없습니다.
  - Azure Disk Encryption 프로세스는 암호화 프로세스의 일환으로 탑재 정보를 /etc/fstab에서 자체의 구성 파일로 이동합니다. 데이터 드라이브 암호화가 완료된 후 /etc/fstab에서 누락된 항목을 보고 놀라지 마세요.
  -  다시 부팅한 후 Azure Disk Encryption 프로세스에서 새로 암호화된 디스크를 탑재하는 데 시간이 걸립니다. 이러한 디스크는 다시 부팅한 후에 즉시 사용할 수 없습니다. 다른 프로세스가 액세스할 수 있게 되기 전에 이 프로세스에서 암호화된 드라이브를 시작, 잠금 해제 및 탑재하는 데 시간이 필요합니다. 이 프로세스는 시스템 특성에 따라 다시 부팅 후 2분 이상 걸릴 수 있습니다.

데이터 디스크를 탑재하고 필요한 /etc/fstab 항목을 만드는 데 사용할 수 있는 명령의 예제는 [이 스크립트 파일의 244-248번째 줄](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248)에 있습니다. 


## <a name="bkmk_GPO"></a> 네트워킹 및 그룹 정책

**Azure Disk Encryption 기능을 사용하도록 설정하려면 IaaS VM에서 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.**
  - Key Vault에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 엔드포인트인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.    


**그룹 정책:**
 - Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup)를 참조하세요.

-  사용자 지정 그룹 정책을 사용하는 도메인 가입 가상 머신의 BitLocker 정책은 다음 설정을 포함해야 합니다. [bitlocker 복구 정보의 사용자 스토리지 구성 -> 256비트 복구 키 허용](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 머신에서 새 정책을 적용하고, 새 정책을 강제로 업데이트한(gpupdate.exe /force) 다음, 다시 시작해야 할 수 있습니다.

- Azure Disk Encryption은 도메인 수준 그룹 정책에는 Bitlocker에서 사용 되는 AES-CBC 알고리즘을 차단 하는 경우 실패 합니다.


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview)은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 브라우저에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 통해 사용하거나 아래 지침에 따라 로컬 머신에 설치하여 PowerShell 세션에서 사용할 수 있습니다. 이미 로컬에 설치되어있는 경우 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성해야 합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases)를 다운로드합니다.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>로컬 머신에서 사용할 Azure PowerShell 설치(선택 사항): 
1. 운영 체제에 대한 링크의 지침을 수행한 다음, 아래의 나머지 단계를 계속 진행합니다.      
   - [Azure PowerShell 설치 및 구성](/powershell/azure/install-az-ps)합니다. 
     - PowerShellGet, Azure PowerShell을 설치 하 고 Az 모듈을 로드 합니다. 

2. Az 모듈의 설치 된 버전을 확인 합니다. 필요한 경우 [Azure PowerShell 모듈을 업데이트](/powershell/azure/install-az-ps#update-the-azure-powershell-module)합니다.
    최신 Az 모듈 버전을 사용 하는 것이 좋습니다.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. 사용 하 여 Azure에 로그인 합니다 [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  필요한 경우 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 검토합니다.

## <a name="bkmk_CLI"></a> 로컬 머신에서 사용할 Azure CLI 설치(선택 사항)

[Azure CLI 2.0](/cli/azure)은 Azure 리소스를 관리하기 위한 명령줄 도구입니다. CLI는 데이터를 유연하게 쿼리하고, 장기 실행 작업을 비차단 프로세스로 지원하고, 쉽게 스크립팅할 수 있도록 설계되었습니다. [Azure Cloud Shell](../cloud-shell/overview.md)과 함께 브라우저에서 사용하거나 로컬 컴퓨터에 설치하여 PowerShell 세션에서 사용할 수 있습니다.

1. 로컬 머신에서 사용할 [Azure CLI 설치](/cli/azure/install-azure-cli)(선택 사항):

2. 설치된 버전을 확인합니다.
     
     ```azurecli-interactive
     az --version
     ``` 

3. [az login](/cli/azure/authenticate-azure-cli)을 사용하여 Azure에 로그인합니다.
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 필요한 경우 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 검토합니다. 


## <a name="prerequisite-workflow-for-key-vault"></a>Key Vault에 대한 필수 구성 요소 워크플로
Azure Disk Encryption에 대한 Key Vault 및 Azure AD 필수 구성 요소에 이미 익숙한 경우 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )를 사용할 수 있습니다. 필수 구성 요소 스크립트 사용에 대한 자세한 내용은 [VM 암호화 빠른 시작](quick-encrypt-vm-powershell.md) 및 [Azure Disk Encryption 부록](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)을 참조하세요. 

1. 필요한 경우 리소스 그룹을 만듭니다.
2. 키 자격 증명 모음을 만듭니다. 
3. 키 자격 증명 모음에 대한 고급 액세스 정책을 설정합니다.

>[!WARNING]
>키 자격 증명 모음을 삭제하기 전에 이를 사용하여 기존 VM을 암호화하지 않았는지 확인합니다. 자격 증명 모음이 실수로 삭제되는 것을 방지하려면 자격 증명 모음에 대해 [일시 삭제 설정](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete)과 [리소스 잠금](../azure-resource-manager/resource-group-lock-resources.md)을 설정합니다. 
 
## <a name="bkmk_KeyVault"></a> Key Vault 만들기 
Azure Disk Encryption은 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)와 통합되어 키 자격 증명 모음 구독에서 디스크 암호화 키 및 비밀을 제어하고 관리할 수 있습니다. Azure Disk Encryption에 대한 키 자격 증명 모음을 만들거나 기존 키 자격 증명 모음을 사용할 수 있습니다. 키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md) 및 [키 자격 증명 모음 보안](../key-vault/key-vault-secure-your-key-vault.md)을 참조하세요. Resource Manager 템플릿, Azure PowerShell 또는 Azure CLI를 사용하여 키 자격 증명 모음을 만들 수 있습니다. 


>[!WARNING]
>암호화 비밀이 지역 경계를 넘지 않도록 하려면 Azure Disk Encryption에서 Key Vault와 VM을 동일한 지역에 공동 배치해야 합니다. 암호화할 VM과 동일한 지역에 있는 Key Vault를 만들고 사용합니다. 


### <a name="bkmk_KVPSH"></a> PowerShell을 사용하여 키 자격 증명 모음 만들기

Azure PowerShell을 사용 하 여 key vault를 만들 수 있습니다 합니다 [새로 만들기-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet. Key Vault에 대 한 추가 cmdlet 참조 [Az.KeyVault](/powershell/module/az.keyvault/)합니다. 

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)합니다. 
2. 필요한 경우 새 리소스 그룹을 만들려면 사용 하 여 [새로 만들기-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)합니다.  목록 데이터 센터 위치를 사용 하 여 [Get AzLocation](/powershell/module/az.resources/get-azlocation)합니다. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. 사용 하 여 새 키 자격 증명 모음 만들기 [AzKeyVault 새로 만들기](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 나중에 디스크를 암호화할 때 사용하기 위해 반환되는 **자격 증명 모음 이름**, **리소스 그룹 이름**, **리소스 ID**, **자격 증명 모음 URI** 및 **개체 ID**를 적어 둡니다. 


### <a name="bkmk_KVCLI"></a> Azure CLI를 사용하여 키 자격 증명 모음 만들기
[az keyvault](/cli/azure/keyvault#commands) 명령을 사용하여 Azure CLI를 통해 키 자격 증명 모음을 관리할 수 있습니다. 키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 사용합니다.

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)합니다.
2. 필요한 경우 [az group create](/cli/azure/group#az-group-create)를 사용하여 새 리소스 그룹을 만듭니다. 위치를 나열하려면 [az account list-locations](/cli/azure/account#az-account-list)를 사용합니다. 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 사용하여 새 키 자격 증명 모음을 만듭니다.
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 나중에 사용하기 위해 반환되는 **자격 증명 모음 이름**(name), **리소스 그룹 이름**, **리소스 ID**(ID), **자격 증명 모음 URI** 및 **개체 ID**를 적어 둡니다. 

### <a name="bkmk_KVRM"></a> Resource Manager 템플릿을 사용하여 키 자격 증명 모음 만들기

[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)을 사용하여 키 자격 증명 모음을 만들 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.
2. 구독, 리소스 그룹, 리소스 그룹 위치, Key Vault 이름, 개체 ID, 약관 및 규약을 선택한 다음, **구매**를 클릭합니다. 


## <a name="bkmk_KVper"></a> 키 자격 증명 모음에 대한 고급 액세스 정책 설정
Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 Key Vault의 암호화 키 또는 비밀에 액세스해야 합니다. 키 자격 증명 모음에서 디스크 암호화를 사용하도록 설정하지 않으면 배포가 실패합니다.  

### <a name="bkmk_KVperPSH"></a> Azure PowerShell을 사용하여 키 자격 증명 모음에 대한 고급 액세스 정책 설정
 키 자격 증명 모음 PowerShell cmdlet을 사용 하 여 [집합 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) key vault에 대 한 디스크 암호화를 사용 하도록 설정 합니다.

  - **디스크 암호화에 Key Vault 사용:** Azure Disk Encryption에 EnabledForDiskEncryption이 필요합니다.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **필요한 경우 템플릿 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 템플릿 배포에서 참조되는 경우 Azure Resource Manager에서 이 키 자격 증명 모음으로부터 비밀을 가져올 수 있도록 합니다.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Azure CLI를 사용하여 키 자격 증명 모음에 대한 고급 액세스 정책 설정
[az keyvault update](/cli/azure/keyvault#az-keyvault-update)를 사용하여 키 자격 증명 모음에 대한 디스크 암호화를 사용하도록 설정합니다. 

 - **디스크 암호화에 Key Vault 사용:** Enabled-for-disk-encryption이 필요합니다. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **필요한 경우 템플릿 배포에 Key Vault 사용:** Resource Manager가 자격 증명 모음에서 비밀을 검색할 수 있습니다.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Azure Portal을 통해 키 자격 증명 모음에 대한 고급 액세스 정책 설정

1. 키 자격 증명 모음을 선택하고, **액세스 정책**, **클릭하여 고급 액세스 정책 표시**로 차례로 이동합니다.
2. **볼륨 암호화를 위해 Azure Disk Encryption에 대한 액세스 사용**이라는 레이블이 있는 상자를 선택합니다.
3. 필요한 경우 **배포를 위해 Azure Virtual Machines에 대한 액세스 사용** 및/또는 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 
4. **저장**을 클릭합니다.

![Azure 키 자격 증명 모음에 대한 고급 액세스 정책](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> 키 암호화 키 설정(선택 사항)
암호화 키에 대한 추가 보안 계층에 KEK(키 암호화 키)를 사용하려면 키 자격 증명 모음에 KEK를 추가합니다. 사용 된 [추가 AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) key vault에 키 암호화 키를 만드는 cmdlet입니다. 또한 온-프레미스 키 관리 HSM에서 KEK를 가져올 수도 있습니다. 자세한 내용은 [Key Vault 설명서](../key-vault/key-vault-hsm-protected-keys.md)를 참조하세요. 키 암호화 키가 지정되면 Azure Disk Encryption에서 해당 키를 사용하여 Key Vault에 쓰기 전에 암호화 비밀을 래핑합니다.

* 키를 생성 하는 경우에 RSA 키 형식을 사용 합니다. Azure Disk Encryption는 타원 곡선 키를 사용 하 여 아직 지원 되지 않습니다.

* Key Vault 비밀 및 KEK URL 버전을 지정해야 합니다. Azure에서 이 버전 관리 제한을 적용합니다. 유효한 비밀과 KEK URL은 다음 예제를 참조하세요.

  * 올바른 비밀 URL의 예제: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 올바른 KEK URL의 예제: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption은 키 자격 증명 모음 비밀 및 KEK URL의 일부로 포트 번호 지정을 지원하지 않습니다. 지원되거나 지원되지 않는 키 자격 증명 모음 URL에 대한 예제는 다음 예제를 참조하세요.

  * 허용되지 않는 키 자격 증명 모음 URL  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 허용 가능한 키 자격 증명 모음 URL  *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Azure PowerShell을 사용하여 키 암호화 키 설정 
PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 Azure Disk Encryption 필수 구성 요소를 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다. 이 스크립트는 모든 Azure Disk Encryption 필수 구성 요소를 만들고, 기존 IaaS VM을 암호화하여 키 암호화 키로 디스크 암호화 키를 래핑합니다. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Windows용 Azure Disk Encryption 사용](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Linux용 Azure Disk Encryption 사용](azure-security-disk-encryption-linux.md)

