---
title: Azure AD Connect cloud 프로 비전 에이전트 gMSA PowerShell cmdlet
description: Azure AD Connect cloud 프로 비전 에이전트 gMSA powershell cmdlet을 사용 하는 방법에 대해 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653799"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect cloud 프로 비전 에이전트 gMSA PowerShell cmdlet

이 문서의 목적은 클라우드 프로 비전 에이전트 gMSA PowerShell cmdlet Azure AD Connect 설명 하는 것입니다. 이러한 cmdlet을 사용 하면 서비스 계정 (gmsa)에 적용 되는 사용 권한에 대 한 세분성을 보다 세부적으로 지정할 수 있습니다. 기본적으로 Azure AD Connect 클라우드 동기화는 기본 gmsa 또는 사용자 지정 gmsa에 Azure AD Connect와 유사한 모든 권한을 적용 합니다. 

이 문서에는 다음과 같은 cmdlet이 포함 되어 있습니다.  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Cmdlet을 사용 하는 방법:  

이러한 cmdlet을 사용 하려면 다음 필수 구성 요소가 필요 합니다.

1. 프로 비전 에이전트를 설치 합니다. 
2. 프로 비전 에이전트 PS 모듈을 PowerShell 세션으로 가져옵니다. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. 기존 사용 권한을 제거 합니다.  셀프 사용을 제외 하 고 서비스 계정에 대 한 기존 사용 권한을 모두 제거 하려면를 사용 `Set-AADCloudSyncRestrictedPermission` 합니다.  

    이 cmdlet에는를 전달할 수 있는 라는 매개 변수가 필요 합니다 .이 매개 변수를 사용 `Credential` 하지 않고 호출 하면 메시지가 표시 됩니다.

    변수 사용을 만들려면  

   `$credential = Get-Credential` 

   그러면 사용자에 게 사용자 이름과 암호를 입력 하 라는 메시지가 표시 됩니다. 자격 증명은 에이전트가 설치 된 도메인의 최소 도메인 관리자 (enterprise admin)에 있어야 합니다. 

4.  그런 다음 cmdlet을 호출 하 여 추가 사용 권한을 제거할 수 있습니다. 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. 또는 간단히 호출할 수 있습니다. 

   `Set-AADCloudSyncRestrictedPermissions` 자격 증명을 묻는 메시지가 표시 됩니다. 

 6.  특정 권한 유형을 추가 합니다.  추가 된 사용 권한은 Azure AD Connect와 동일 합니다.  권한 설정에 대 한 예제는 아래의 [AADCloudSyncPermissions 사용](#using-set-aadcloudsyncpermissions) 을 참조 하세요.

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions 사용 
`Set-AADCloudSyncPermissions` 는 Azure AD Connect에서 사용 하는 권한과 동일한 다음과 같은 사용 권한 유형을 지원 합니다. 다음 사용 권한 유형이 지원 됩니다. 

|사용 권한 유형|설명|
|-----|-----|
|BasicRead| Azure AD Connect에 대 한 [Basicread](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) 권한을 참조 하세요.|
|PasswordHashSync|Azure AD Connect [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) 권한을 참조 하세요.|
|PasswordWriteBack|Azure AD Connect에 대 한 [암호 쓰기 저장](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) 권한 참조|
|HybridExchangePermissions|Azure AD Connect [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) 권한을 참조 하세요.| 
|ExchangeMailPublicFolderPermissions| Azure AD Connect [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) 권한을 참조 하세요.| 
|CloudHR| ' 하위 사용자 개체 '에 ' 모든 권한 '을 적용 하 고 '이 개체 및 모든 하위 개체 '에서 ' 사용자 개체 만들기/삭제 '를 적용 합니다.| 
|모두|위의 모든 권한을 추가 합니다.| 



다음 두 가지 방법 중 하나로 AADCloudSyncPermissions를 사용할 수 있습니다.
- [구성 된 모든 도메인에 특정 사용 권한 부여](#grant-a-certain-permission-to-all-configured-domains) 
- [특정 도메인에 특정 사용 권한 부여](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>구성 된 모든 도메인에 특정 사용 권한 부여 
구성 된 모든 도메인에 특정 사용 권한을 부여 하려면 엔터프라이즈 관리자 계정을 사용 해야 합니다.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>특정 도메인에 특정 사용 권한 부여 
특정 도메인에 특정 사용 권한을 부여 하려면 최소한 추가 하려는 도메인의 도메인 관리자 계정을 사용 해야 합니다.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

참고: 1의 경우 자격 증명은 최소한의 엔터프라이즈 관리자 여야 합니다. 

2의 경우 자격 증명은 도메인 관리자 또는 엔터프라이즈 관리자 중 하나일 수 있습니다. 

  

