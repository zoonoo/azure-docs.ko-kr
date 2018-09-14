---
title: 여러 응용 프로그램에 Azure Key Vault 액세스 권한 부여 | Microsoft Docs
description: 여러 응용 프로그램에 Key Vault 액세스 권한을 부여하는 방법을 알아봅니다.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 421ceca1453b9e3b97c5ede520ec92372baf2020
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299664"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>여러 응용 프로그램에 Key Vault 액세스 권한 부여

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>Q: Key Vault에 액세스해야 하는 여러 응용 프로그램이 있습니다. 이러한 응용 프로그램(최대 1024개)에 Key Vault에 대한 액세스 권한을 부여하려면 어떻게 해야 하나요?

Key Vault 액세스 제어 정책은 최대 1024개 항목을 지원합니다. 그러나 Azure Active Directory 보안 그룹을 만들 수 있습니다. 관련된 모든 서비스 주체를 이 보안 그룹에 추가한 다음 Key Vault에 이 보안 그룹에 대한 액세스를 허용합니다.

다음은 필수 조건입니다.
* [Azure Active Directory V2 PowerShell 모듈 설치](https://www.powershellgallery.com/packages/AzureAD)
* [Azure PowerShell 설치](/powershell/azure/overview)
* 다음 명령을 실행하려면 Azure Active Directory 테넌트의 그룹 만들기/편집 권한이 필요합니다. 권한이 없는 경우 Azure Active Directory 관리자에게 문의합니다.

이제 PowerShell에서 다음 명령을 실행합니다.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

응용 프로그램 그룹에 다른 권한 집합을 부여해야 할 경우 이러한 응용 프로그램에 대해 별도의 Azure Active Directory 보안 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 보안 설정](key-vault-secure-your-key-vault.md)에 대해 좀 더 자세히 알아봅니다.
