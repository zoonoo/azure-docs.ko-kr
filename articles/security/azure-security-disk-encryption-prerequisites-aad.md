---
title: Azure AD 앱을 포함한 Azure Disk Encryption 필수 구성 요소(이전 릴리스)
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption을 사용하기 위한 필수 구성 요소를 설명합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 201998168b0709b1608ffad2565518e15d47e52c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234296"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Azure Disk Encryption 필수 구성 요소(이전 릴리스)

**Azure Disk Encryption의 새 릴리스는 Azure AD 애플리케이션 매개 변수 제공에 대한 요구 사항을 제거하여 VM 디스크 암호화를 사용하도록 설정합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 모든 새 VM은 새 릴리스를 사용하는 Azure AD 애플리케이션 매개 변수를 사용하지 않고 암호화되어야 합니다. 새 릴리스를 사용하여 VM 디스크 암호화를 설정하는 방법을 보려면 [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)을 참조하세요. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 AAD 구문을 사용하여 계속 유지 관리되어야 합니다.**

 'Azure Disk Encryption 필수 구성 요소'라는 이 문서에서는 Azure Disk Encryption을 사용하기 전에 필요한 항목에 대해 설명합니다. 일반적인 필수 구성 요소와 함께 Azure Disk Encryption은 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)와 통합되고, 키 자격 증명 모음의 암호화 키를 관리하기 위해 Azure AD 애플리케이션을 사용하여 인증을 제공합니다. 또한 [Azure PowerShell](/powershell/azure/overview) 또는 [Azure CLI](/cli/azure/)를 사용하여 Key Vault 및 Azure AD 애플리케이션을 설정하거나 구성할 수 있습니다.

[Azure Disk Encryption 개요](azure-security-disk-encryption-overview.md) 문서에서 설명한 지원되는 시나리오에 대해 Azure IaaS VM에서 Azure Disk Encryption을 사용하도록 설정하려면 먼저 필수 구성 요소가 준비되어 있어야 합니다. 

> [!WARNING]
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.
> - 이전에 [Azure AD 앱에서 Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md)를 사용하여 이 VM을 암호화한 경우에는 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 이는 지원되는 시나리오가 아니므로 이 암호화된 VM에서는 [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)을 사용할 수 없습니다. 즉, 이 암호화된 VM을 위해 AAD 애플리케이션에서 전환하는 기능은 아직 지원되지 않습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> 지원되는 운영 체제
Azure Disk Encryption이 지원되는 운영 체제는 다음과 같습니다.

- Windows Server 버전: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016
  - Windows Server 2008 R2의 경우 Azure에서 암호화를 사용하도록 설정하기 전에 .NET Framework 4.5를 설치해야 합니다. Windows 업데이트에서 선택적 업데이트인 Windows Server 2008 R2 x64 기반 시스템용 Microsoft .NET Framework 4.5.2([KB2901983](https://support.microsoft.com/kb/2901983))를 사용하여 설치합니다.    
- Windows 클라이언트 버전: Windows 8 클라이언트 및 Windows 10 클라이언트
- Azure Disk Encryption은 특정 Azure 갤러리 기반 Linux 서버 배포판 및 버전에서 지원됩니다. 현재 지원되는 버전 목록은 [Azure Disk Encryption FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)를 참조하세요.
- Azure Disk Encryption은 Key Vault 및 VM이 동일한 Azure 하위 지역 및 구독에 있어야 합니다. 별도 하위 지역에서 리소스를 구성하면 Azure Disk Encryption 기능 사용 시 오류가 발생합니다.

## <a name="bkmk_LinuxPrereq"></a> Linux IaaS VM에 대한 추가 필수 구성 요소 

- Linux용 Azure Disk Encryption에는 [지원되는 이미지](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)에서 OS 디스크 암호화를 사용하도록 설정하기 위해 VM에 7GB의 RAM이 필요합니다. OS 디스크 암호화 프로세스가 완료되면 VM을 적은 메모리로 실행하도록 구성할 수 있습니다.
- 암호화를 사용하도록 설정하기 전에 암호화할 데이터 디스크를 /etc/fstab에 올바르게 나열해야 합니다. "/dev/sdX" 형식의 디바이스 이름은 특히 암호화가 적용된 후 다시 부팅할 때 동일한 디스크에 연결될 수 없으므로 이 항목에 영구 블록 디바이스 이름을 사용합니다. 이 동작에 대한 자세한 내용은 [Linux VM 디바이스 이름 변경 문제 해결](../virtual-machines/linux/troubleshoot-device-names-problems.md)을 참조하세요.
- /etc/fstab 설정이 탑재에 대해 올바르게 구성되었는지 확인합니다. 이러한 설정을 구성하려면 mount -a 명령을 실행하거나 VM을 다시 부팅하고 다시 탑재를 트리거합니다. 이러한 작업이 완료되면 lsblk 명령의 출력을 확인하여 원하는 드라이브가 여전히 탑재되어 있는지 확인합니다. 
  - 암호화를 사용하도록 설정하기 전에 /etc/fstab 파일에서 드라이브를 올바르게 탑재하지 않으면 Azure Disk Encryption에서 해당 드라이브를 올바르게 탑재할 수 없습니다.
  - Azure Disk Encryption 프로세스는 암호화 프로세스의 일환으로 탑재 정보를 /etc/fstab에서 자체의 구성 파일로 이동합니다. 데이터 드라이브 암호화가 완료된 후 /etc/fstab에서 누락된 항목을 보고 놀라지 마세요.
  -  다시 부팅한 후 Azure Disk Encryption 프로세스에서 새로 암호화된 디스크를 탑재하는 데 시간이 걸립니다. 이러한 디스크는 다시 부팅한 후 즉시 사용할 수 없습니다. 다른 프로세스가 액세스할 수 있게 되기 전에 이 프로세스에서 암호화된 드라이브를 시작, 잠금 해제 및 탑재하는 데 시간이 필요합니다. 이 프로세스는 시스템 특성에 따라 다시 부팅 후 2분 이상 걸릴 수 있습니다.

데이터 디스크를 탑재하고 필요한 /etc/fstab 항목을 만드는 데 사용할 수 있는 명령의 예제는 [이 스크립트 파일의 197-205번째 줄](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205)에 있습니다. 


## <a name="bkmk_GPO"></a> 네트워킹 및 그룹 정책

**기존 AAD 매개 변수 구문을 사용하여 Azure Disk Encryption 기능을 사용하도록 설정하려면 IaaS VM이 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.** 
  - Key Vault에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 엔드포인트인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.
  - Windows에서 TLS 1.0이 명시적으로 비활성화되고 .NET 버전이 4.6 이상으로 업데이트되지 않은 경우 다음과 같이 레지스트리 변경 시 ADE가 최신 TLS 버전을 선택하도록 설정됩니다.
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**그룹 정책:**
 - Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup)를 참조하세요.

-  사용자 지정 그룹 정책을 사용하는 도메인 가입 가상 머신의 BitLocker 정책은 다음 설정을 포함해야 합니다. [사용자 저장소 구성의 BitLocker 복구 정보 허용 256 비트 복구 키->](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)합니다. BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 머신에서 새 정책을 적용하고, 새 정책을 강제로 업데이트한(gpupdate.exe /force) 다음, 다시 시작해야 할 수 있습니다.  


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview)은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 브라우저에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 통해 사용하거나 아래 지침에 따라 로컬 머신에 설치하여 PowerShell 세션에서 사용할 수 있습니다. 이미 있는 경우 로컬로 설치를 최신 버전의 Azure PowerShell을 사용 하 여 Azure Disk Encryption을 구성 해야 합니다.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>로컬 머신에서 사용할 Azure PowerShell 설치(선택 사항):  
1. [Azure PowerShell 설치 및 구성](/powershell/azure/install-az-ps)합니다. 

2. [Azure Active Directory PowerShell 모듈](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module)을 설치합니다. 

     ```powershell
     Install-Module AzureAD
     ```

3. 설치된 모듈 버전을 확인합니다.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. 사용 하 여 Azure에 로그인 합니다 [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Azure AD에 연결합니다([Connect-AzureAD](/powershell/module/azuread/connect-azuread)).
     
     ```powershell
     Connect-AzureAD
     ```

6. 필요한 경우 [Azure PowerShell 시작](/powershell/azure/get-started-azureps) 및 [AzureAD](/powershell/module/azuread)를 검토합니다.

## <a name="bkmk_CLI"></a> Azure CLI

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


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Key Vault 및 Azure AD 응용 프로그램에 대한 필수 구성 요소 워크플로

Azure Disk Encryption에 대한 Key Vault 및 Azure AD 필수 구성 요소에 이미 익숙한 경우 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )를 사용할 수 있습니다. 필수 구성 요소 스크립트 사용에 대한 자세한 내용은 [VM 암호화 빠른 시작](quick-encrypt-vm-powershell.md) 및 [Azure Disk Encryption 부록](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)을 참조하세요. 

1. 키 자격 증명 모음을 만듭니다. 
2. Azure AD 애플리케이션 및 서비스 주체를 설정합니다.
3. Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책을 설정합니다.
4. 키 자격 증명 모음에 대한 고급 액세스 정책을 설정합니다.
 
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


## <a name="bkmk_ADapp"></a> Azure AD 응용 프로그램 및 서비스 주체 설정 
Azure에서 실행 중인 VM에서 암호화를 사용하도록 설정해야 하는 경우 Azure Disk Encryption이 암호화 키를 생성하고 Key Vault에 기록합니다. Key Vault에서 암호화 키를 관리하려면 Azure AD 인증이 필요합니다. 이를 위해 Azure AD 애플리케이션을 만듭니다. 인증을 위해 클라이언트 비밀 기반 인증 또는 [클라이언트 인증서 기반 Azure AD 인증](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)을 사용할 수 있습니다.


### <a name="bkmk_ADappPSH"></a> Azure PowerShell을 사용하여 Azure AD 응용 프로그램 및 서비스 주체 설정 
다음 명령을 실행하려면 [Azure AD PowerShell 모듈](/powershell/azure/active-directory/install-adv2)을 가져와 사용합니다. 

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)합니다.
2. 사용 하 여는 [새로 만들기-AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet을 Azure AD 응용 프로그램을 만듭니다. MyApplicationHomePage 및 MyApplicationUri는 원하는 값이 될 수 있습니다.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId는 Azure AD ClientID이고, $aadClientSecret은 나중에 Azure Disk Encryption을 사용하도록 설정하는 데 사용할 클라이언트 비밀입니다. Azure AD 클라이언트 비밀을 적절하게 보호합니다. `$azureAdApplication.ApplicationId`를 실행하면 ApplicationID가 표시됩니다.


### <a name="bkmk_ADappCLI"></a> Azure CLI를 사용하여 Azure AD 응용 프로그램 및 서비스 주체 설정

[az ad sp](/cli/azure/ad/sp) 명령을 사용하여 Azure CLI를 통해 서비스 주체를 관리할 수 있습니다. 자세한 내용은 [Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)합니다.

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)합니다.
2. 새 서비스 주체를 만듭니다.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  반환된 appId는 다른 명령에 사용되는 Azure AD ClientID이며, az keyvault set-policy에 사용할 SPN이기도 합니다. 암호는 나중에 Azure Disk Encryption을 사용하도록 설정하는 데 사용해야 하는 클라이언트 비밀입니다. Azure AD 클라이언트 비밀을 적절하게 보호합니다.
 
### <a name="bkmk_ADappRM"></a> Azure Portal을 통해 Azure AD 응용 프로그램 및 서비스 주체 설정
[포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 문서의 단계를 사용하여 Azure AD 애플리케이션을 만듭니다. 아래에 나열된 각 단계는 수행할 문서 섹션으로 바로 이동합니다. 

1. [필요한 권한 확인](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Azure Active Directory 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - 애플리케이션을 만들 때 원하는 이름과 로그온 URL을 사용할 수 있습니다.
3. [애플리케이션 ID 및 인증 키 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) 
     - 인증 키 클라이언트 암호 이며 집합 AzVMDiskEncryptionExtension는 AadClientSecret으로 사용 됩니다. 
        - 인증 키는 애플리케이션에서 Azure AD에 로그인하기 위한 자격 증명으로 사용됩니다. Azure Portal에서 이 비밀은 키라고 하지만, 키 자격 증명 모음과는 아무런 관련이 없습니다. 이 비밀을 적절하게 보호하세요. 
     - 응용 프로그램 ID AzKeyVaultAccessPolicy 집합에 대 한 ServicePrincipalName AzVMDiskEncryptionExtension 집합에 대 한 AadClientId와 나중에 사용 됩니다. 

## <a name="bkmk_KVAP"></a> Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정
지정된 Key Vault에 암호화 비밀을 쓰려면 Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure Active Directory 애플리케이션의 클라이언트 ID와 클라이언트 비밀이 Azure Disk Encryption에 필요합니다. 

> [!NOTE]
> Azure Disk Encryption에서는 Azure AD 클라이언트 애플리케이션에 _WrapKey_ 및 _Set_ 권한과 같은 액세스 정책을 구성해야 합니다.

### <a name="bkmk_KVAPPSH"></a> Azure PowerShell을 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정
Azure AD 애플리케이션에 자격 증명 모음의 키 또는 암호에 액세스할 권한이 필요합니다. 사용 합니다 [집합 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet으로 클라이언트 ID (응용 프로그램 등록 되었을 때 생성 된)를 사용 하 여 응용 프로그램에 권한을 부여 하는 _– ServicePrincipalName_ 매개 변수 값입니다. 자세한 내용은 블로그 게시물 [Azure Key Vault - 단계별](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)을 참조하세요. 

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)합니다.
2. PowerShell을 사용하여 AD 애플리케이션에 대한 키 자격 증명 모음 액세스 정책을 설정합니다.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Azure CLI를 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정
[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)를 사용하여 액세스 정책을 설정합니다. 자세한 내용은 [CLI 2.0을 사용하여 Key Vault 관리](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)를 참조하세요.

1. 필요한 경우 [Azure 구독에 연결](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)합니다.
2. 다음 명령을 사용하여 비밀 및 래핑 키를 가져오려면 Azure CLI 액세스를 통해 만든 서비스 주체에게 액세스 권한을 부여합니다.
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> 포털을 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정

1. 키 자격 증명 모음을 사용하여 리소스 그룹을 엽니다.
2. 키 자격 증명 모음을 선택하고, **액세스 정책**으로 이동한 다음, **새로 추가**를 클릭합니다.
3. **보안 주체 선택** 아래에서 만든 Azure AD 애플리케이션을 검색하여 선택합니다. 
4. **키 권한**에 대해 **암호화 작업** 아래에서 **키 래핑**을 선택합니다.
5. **비밀 권한**에 대해 **비밀 관리 작업** 아래에서 **설정**을 선택합니다.
6. **확인**을 클릭하여 액세스 정책을 저장합니다. 

![Azure Key Vault 암호화 작업 - 키 래핑](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 비밀 권한 - 설정](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

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

 - **필요한 경우 배포에 Key Vault 사용:** Virtual Machines에서 자격 증명 모음으로부터 비밀로 저장된 인증서를 검색할 수 있도록 허용합니다.
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
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> 인증서 기반 인증(선택 사항)
인증서 인증을 사용하려면 키 자격 증명 모음에 인증서를 업로드하고 이 인증서를 클라이언트에 배포할 수 있습니다. PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 Azure Disk Encryption 필수 구성 요소를 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> 인증서 기반 인증 및 KEK(선택 사항)

인증서 인증을 사용하고 KEK로 암호화 키를 래핑하려면 아래 스크립트를 예로 사용할 수 있습니다. PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 이전의 Azure Disk Encryption 필수 구성 요소를 모두 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다.

> [!IMPORTANT]
> Azure AD 인증서 기반 인증은 현재 Linux VM에서 지원되지 않습니다.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Windows용 Azure Disk Encryption 사용](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Linux용 Azure Disk Encryption 사용](azure-security-disk-encryption-linux-aad.md)
