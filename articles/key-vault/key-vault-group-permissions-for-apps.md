---
title: 여러 애플리케이션에 Azure Key Vault에 대한 액세스 권한 부여 - Azure Key Vault | Microsoft Docs
description: 여러 애플리케이션에 Key Vault 액세스 권한을 부여하는 방법을 알아봅니다.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 39624595b4dd4e72ab1d34082a6ba44a1b3636d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721984"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>키 자격 증명 모음에 여러 애플리케이션 액세스 권한 부여

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

액세스 제어 정책을 사용하여 키 자격 증명 모음에 다수의 애플리케이션 액세스 권한을 부여할 수 있습니다. 액세스 제어 정책은 최대 1024개 애플리케이션을 지원할 수 있으며 다음과 같이 구성됩니다.

1. Azure Active Directory 보안 그룹을 만듭니다. 
2. 모든 애플리케이션의 연관된 서비스 주체를 보안 그룹에 추가합니다.
3. Key Vault에 보안 그룹 액세스 권한을 부여합니다.

## <a name="prerequisites"></a>필수 조건

다음은 필수 조건입니다.
* [Azure PowerShell 설치](/powershell/azure/overview)
* [Azure Active Directory V2 PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)을 설치합니다.
* Azure Active Directory 테넌트에서 그룹을 생성/편집할 수 있는 권한입니다. 권한이 없는 경우 Azure Active Directory 관리자에게 문의합니다. Key Vault 액세스 정책 권한에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 정보](about-keys-secrets-and-certificates.md)를 참조하세요.

## <a name="granting-key-vault-access-to-applications"></a>애플리케이션에 Key Vault 액세스 권한 부여

PowerShell에서 다음 명령을 실행합니다.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

애플리케이션 그룹에 다른 권한 집합을 부여해야 할 경우 이러한 애플리케이션에 대해 별도의 Azure Active Directory 보안 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 보안 설정](key-vault-secure-your-key-vault.md)에 대해 좀 더 자세히 알아봅니다.
