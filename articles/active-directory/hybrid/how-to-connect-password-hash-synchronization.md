---
title: Azure AD Connect 동기화로 암호 해시 동기화 구현 | Microsoft Docs
description: 암호 해시 동기화 작동 방식 및 설정 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5ca62bc032c12c568e2b8065630dcd8b687513
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483108"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현
이 문서에서는 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure Active Directory(Azure AD) 인스턴스로 사용자 암호를 동기화하는 데 필요한 정보를 제공합니다.

## <a name="how-password-hash-synchronization-works"></a>암호 해시 동기화 작동 방식
Active Directory 도메인 서비스는 실제 사용자 암호의 해시 값 표현 형태로 암호를 저장합니다. 해시 값은 단방향 수학 함수(*해시 알고리즘*)의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 

암호를 동기화 하기 위해, Azure AD Connect 동기화는 온-프레미스 Active Directory 인스턴스에서 암호 해시를 추출합니다. Azure Active Directory 인증 서비스로 동기화 되기 전에 암호 해시에 추가적인 보안 처리가 적용됩니다. 암호는 각 사용자 기본별로 동기화되고 시간 순서대로 동기화됩니다.

암호 해시 동기화 과정의 실제 데이터 흐름은 사용자 데이터의 동기화와 비슷합니다. 그러나 암호는 다른 특성에 대한 표준 디렉터리 동기화 창보다 더 자주 동기화됩니다. 암호 해시 동기화 프로세스는 2분마다 실행됩니다. 이 프로세스의 빈도를 수정할 수 없습니다. 암호를 동기화할 경우 기존 클라우드 암호를 덮어씁니다.

암호 해시 동기화 기능을 사용하도록 처음으로 설정하면 범위 내 모든 사용자 암호에 대한 초기 동기화를 수행합니다. 동기화할 사용자 암호의 하위 집합을 명시적으로 정의할 수 없습니다. However, if there are multiple connectors, it is possible to disable password hash sync for some connectors but not others using the [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) cmdlet.

온-프레미스 암호를 변경하면, 업데이트된 암호는 대개 몇 분 내에 동기화됩니다.
암호 해시 동기화 기능은 실패한 사용자 암호 동기화를 자동으로 다시 시도합니다. 암호를 동기화하는 동안 오류가 발생하면 이벤트 뷰어에 오류가 기록됩니다.

암호 동기화는 현재 로그인된 사용자에게 아무런 영향도 미치지 않습니다.
클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 서비스 세션이 즉시 영향을 받지는 않습니다. 하지만, 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

사용자는 회사 네트워크에 로그인되어 있는지 여부에 관계없이 Azure AD에 인증하려면 회사 자격 증명을 다시 입력해야 합니다. 하지만 사용자가 로그인 시 KMSI(로그인 유지) 확인란을 선택하면 이 패턴을 최소화할 수 있습니다. 이 확인란을 선택하면 180일 동안 인증을 우회하는 세션 쿠키가 설정됩니다. KMSI 동작은 Azure AD 관리자가 설정 또는 해제할 수 있습니다. 또한 회사 네트워크에 연결된 회사 디바이스에 있을 때 사용자가 자동으로 로그인되는 [Seamless SSO](how-to-connect-sso.md)를 켜서 암호 프롬프트를 줄일 수 있습니다.

> [!NOTE]
> 암호 동기화는 Active Directory의 개체 형식 사용자에만 지원됩니다. iNetOrgPerson 개체 형식에 대해 지원되지 않습니다.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>암호 해시 동기화의 작동 방식에 대한 자세한 설명

다음 섹션에서는 Active Directory와 Azure AD 간에 암호 해시 동기화가 작동하는 방식에 대해 자세히 설명합니다.

![자세한 암호 흐름](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. 2분마다 AD Connect 서버의 암호 해시 동기화 에이전트는 DC에서 저장된 암호 해시(unicodePwd 특성)를 요청합니다.  이 요청은 DC 간 데이터를 동기화하는 데 사용된 표준 [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) 복제 프로토콜을 통해 전송됩니다. 암호 해시를 얻으려면 서비스 계정에 디렉터리 변경 내용 복제 및 모든 디렉터리 변경 내용 복제 AD 권한(설치 시 기본적으로 부여)이 있어야 합니다.
2. 전송하기 전에 DC는 RPC 세션 키의 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) 해시인 키와 솔트를 사용하여 MD4 암호 해시를 암호화합니다. 그런 다음, RPC를 통해 암호 해시 동기화 에이전트에 결과를 전송합니다. 또한 DC는 DC 복제 프로토콜을 사용하여 동기화 에이전트에 솔트를 전달하고, 따라서 에이전트는 봉투(Envelope)를 해독할 수 있게 됩니다.
3. 암호 해시 동기화 에이전트는 봉투(Envelope)를 암호화한 후 [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx)와 솔트를 사용하여 수신한 데이터를 원래 MD4 형식으로 해독하는 키를 생성합니다. 암호 해시 동기화 에이전트는 절대 일반 텍스트 암호에 액세스할 수 없습니다. 암호 해시 동기화 에이전트의 MD5 사용은 DC와의 복제 프로토콜 호환성으로 그 용도가 엄격하게 제한되며 DC와 암호 해시 동기화 에이전트 간의 온-프레미스에서만 사용됩니다.
4. 암호 해시 동기화 에이전트는 해시를 32바이트 16진수 문자열로 변환한 후 UTF-16 인코딩을 사용하여 이 문자열을 다시 이진으로 변환하는 방법으로 16바이트 이진 암호 해시를 64바이트로 확장합니다.
5. 암호 해시 동기화 에이전트는 10바이트 길이 솔트로 구성된 사용자별 솔트를 64비트 이진 파일에 추가하여 원래 해시의 보안을 강화합니다.
6. 그런 다음, 암호 해시 동기화 에이전트는 MD4 해시와 사용자별 솔트를 결합하고 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 함수에 입력합니다. [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 키 지정 해시 알고리즘이 1000번 반복됩니다. 
7. 암호 해시 동기화 에이전트는 결과 32바이트 해시를 가져오고, 사용자별 솔트와 SHA256 반복 횟수를 해시에 연결하고(Azure AD가 사용할 수 있도록), SSL을 통해 Azure AD Connect에서 Azure AD로 문자열을 전송합니다.</br> 
8. 사용자가 Azure AD에 로그인을 시도하고 암호를 입력하면 암호가 동일한 MD4+salt+PBKDF2+HMAC-SHA256 프로세스를 통해 실행됩니다. 결과 해시가 Azure AD에 저장된 해시와 일치하고 사용자가 올바른 암호를 입력하면 해당 사용자가 인증됩니다.

> [!NOTE]
> 원래 MD4 해시는 Azure AD로 전송되지 않습니다. 대신 원래 MD4 해시의 SHA256 해시가 전송됩니다. 결과적으로 Azure AD에 저장된 해시를 습득하더라도 온-프레미스 pass-the-hash 공격에 사용할 수 없습니다.

### <a name="security-considerations"></a>보안 고려 사항

암호를 동기화할 때, 사용자 암호의 일반 텍스트 버전은 암호 해시 동기화 기능, Azure AD 혹은 다른 어떤 관련 서비스에 노출되지 않습니다.

사용자 인증은 조직의 고유한 Active Directory 인스턴스가 아닌 Azure AD에 대해 이루어집니다. Azure AD에 저장된 SHA256 암호 데이터(원래 MD4 해시의 해시)는 Active Directory에 저장된 것보다 안전합니다. 뿐만 아니라 이 SHA256 해시는 해독이 불가능하기 때문에 조직의 Active Directory 환경으로 가져와서 pass-the-hash 공격에서 유효한 사용자 암호로 표시할 수 없습니다.

### <a name="password-policy-considerations"></a>암호 정책 고려 사항

암호 해시 동기화를 사용할 경우, 영향을 받는 두 가지 정책이 있습니다.

* 암호 복잡성 정책
* 암호 만료 정책

#### <a name="password-complexity-policy"></a>암호 복잡성 정책

암호 해시 동기화를 사용하는 경우 온-프레미스 Active Directory 인스턴스의 암호 복잡성 정책이 동기화된 사용자에 대한 클라우드의 복잡성 정책을 재정의합니다. 온-프레미스 Active Directory 인스턴스의 유효한 모든 암호를 사용하여 Azure AD 서비스에 액세스할 수 있습니다.

> [!NOTE]
> 클라우드에서 직접 만든 사용자의 암호는 클라우드 내에서 정의된 암호 정책을 계속 따릅니다.

#### <a name="password-expiration-policy"></a>암호 만료 정책

If a user is in the scope of password hash synchronization, by default the cloud account password is set to *Never Expire*.

온-프레미스 환경에서 만료된 동기화된 암호를 사용하여 클라우드 서비스에 계속 로그인할 수 있습니다. 클라우드 암호는 온-프레미스 환경에서 다음에 암호를 변경할 때 업데이트됩니다.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Public preview of the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature

If there are synchronized users that only interact with Azure AD integrated services and must also comply with a password expiration policy, you can force them to comply with your Azure AD password expiration policy by enabling the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature.

When *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is disabled (which is the default setting), Azure AD Connect sets the PasswordPolicies attribute of synchronized users to "DisablePasswordExpiration". This is done every time a user's password is synchronized and instructs Azure AD to ignore the cloud password expiration policy for that user. You can check the value of the attribute using the Azure AD PowerShell module with the following command:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


To enable the EnforceCloudPasswordPolicyForPasswordSyncedUsers feature, run the following command using the MSOnline PowerShell module:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

Once enabled, Azure AD does not go to each synchronized user to remove the `DisablePasswordExpiration` value from the PasswordPolicies attribute. Instead, the value is set to `None` during the next password sync for each user when they next change their password in on-premises AD.  

It is recommended to enable EnforceCloudPasswordPolicyForPasswordSyncedUsers, prior to enabling password hash sync, so that the initial sync of password hashes does not add the `DisablePasswordExpiration` value to the PasswordPolicies attribute for the users.

The default Azure AD password policy requires users to change their passwords every 90 days. If your policy in AD is also 90 days, the two policies should match. However, if the AD policy is not 90 days, you can update the Azure AD password policy to match by using the Set-MsolPasswordPolicy PowerShell command.

Azure AD supports a separate password expiration policy per registered domain.

Caveat: If there are synchronized accounts that need to have non-expiring passwords in Azure AD, you must explicitly add the `DisablePasswordExpiration` value to the PasswordPolicies attribute of the user object in Azure AD.  You can do this by running the following command.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Public Preview of synchronizing temporary passwords and "Force Password on Next Logon"

It is typical to force a user to change their password during their first logon, especially after an admin password reset occurs.  It is commonly known as setting a "temporary" password and is completed by checking the "User must change password at next logon" flag on a user object in Active Directory (AD).
  
The temporary password functionality helps to ensure that the transfer of ownership of the credential is completed on first use, to minimize the duration of time in which more than one individual has knowledge of that credential.

To support temporary passwords in Azure AD for synchronized users, you can enable the *ForcePasswordResetOnLogonFeature* feature, by running the following command on your Azure AD Connect server, replacing <AAD Connector Name> with the connector name specific to your environment:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

You can use the following command to determine the connector name:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

Caveat:  Forcing a user to change their password on next logon requires a password change at the same time.  AD Connect will not pick up the force password change flag by itself, it is supplemental to the detected password change that occurs during password hash sync.

> [!CAUTION]
> If you do not enable Self-service Password Reset (SSPR) in Azure AD users will have a confusing experience when they reset their password in Azure AD and then attempt to sign in in Active Directory with the new password, as the new password isn’t valid in Active Directory. You should only use this feature when SSPR and Password Writeback is enabled on the tenant.

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="account-expiration"></a>계정 만료

조직에서 사용자 계정 관리의 일부로 accountExpires 특성을 사용하는 경우 이 특성은 Azure AD와 동기화되지 않습니다. 결과적으로 암호 해시 동기화를 위해 구성된 환경의 만료된 Active Directory 계정은 Azure AD에서 계속 활성 상태입니다. 계정이 만료되면 워크플로 작업에서 사용자의 Azure AD 계정을 비활성화하는 PowerShell 스크립트를 트리거하는([Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) cmdlet를 사용하는) 것이 좋습니다. 반대로 계정이 켜지면 Azure AD 인스턴스도 켜져야 합니다.

### <a name="overwrite-synchronized-passwords"></a>동기화된 암호 덮어쓰기

관리자는 Windows PowerShell을 사용하여 사용자 암호를 수동으로 재설정할 수 있습니다.

이런 경우, 새 암호는 사용자의 동기화된 암호를 재정의하고 클라우드 내에 정의된 모든 암호 정책이 새 암호에 적용됩니다.

사용자가 온-프레미스 암호를 다시 변경하면, 새 암호는 클라우드에 동기화되며, 수동으로 업데이트한 암호를 재정의합니다.

암호 동기화는 로그인된 Azure 사용자에게 아무런 영향도 미치지 않습니다. 클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 세션이 즉시 영향을 받지는 않습니다. KMSI는 이런 차이가 나는 기간을 연장합니다. 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

### <a name="additional-advantages"></a>추가적인 이점

- 일반적으로 암호 해시 동기화는 페더레이션 서비스보다 더 쉽게 구현할 수 있습니다. 추가 서버가 필요 없으며, 사용자를 인증하기 위해 고가용성의 페더레이션 서비스에 의존하지 않아도 됩니다.
- 페더레이션 외에 암호 해시 동기화를 사용하도록 설정할 수도 있습니다. 페더레이션 서비스에 중단이 발생하는 경우 대체 서비스로 사용될 수 있습니다.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Password hash sync process for Azure AD Domain Services

If you use Azure AD Domain Services to provide legacy authentication for applications and services that need to use Keberos, LDAP, or NTLM, some additional processes are part of the password hash synchronization flow. Azure AD Connect uses the additional following process to synchronize password hashes to Azure AD for use in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect should only be installed and configured for synchronization with on-premises AD DS environments. It's not supported to install Azure AD Connect in an Azure AD DS managed domain to synchronize objects back to Azure AD.
>
> Azure AD Connect only synchronizes legacy password hashes when you enable Azure AD DS for your Azure AD tenant. The following steps aren't used if you only use Azure AD Connect to synchronize an on-premises AD DS environment with Azure AD.
>
> If your legacy applications don't use NTLM authentication or LDAP simple binds, we recommend that you disable NTLM password hash synchronization for Azure AD DS. For more information, see [Disable weak cipher suites and NTLM credential hash synchronization](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect retrieves the public key for the tenant's instance of Azure AD Domain Services.
1. When a user changes their password, the on-premises domain controller stores the result of the password change (hashes) in two attributes:
    * *unicodePwd* for the NTLM password hash.
    * *supplementalCredentials* for the Kerberos password hash.
1. Azure AD Connect detects password changes through the directory replication channel (attribute changes needing to replicate to other domain controllers).
1. For each user whose password has changed, Azure AD Connect performs the following steps:
    * Generates a random AES 256-bit symmetric key.
    * Generates a random initialization vector needed for the first round of encryption.
    * Extracts Kerberos password hashes from the *supplementalCredentials* attributes.
    * Checks the Azure AD Domain Services security configuration *SyncNtlmPasswords* setting.
        * If this setting is disabled, generates a random, high-entropy NTLM hash (different from the user's password). This hash is then combined with the exacted Kerberos password hashes from the *supplementalCrendetials* attribute into one data structure.
        * If enabled, combines the value of the *unicodePwd* attribute with the extracted Kerberos password hashes from the *supplementalCredentials* attribute into one data structure.
    * Encrypts the single data structure using the AES symmetric key.
    * Encrypts the AES symmetric key using the tenant's Azure AD Domain Services public key.
1. Azure AD Connect transmits the encrypted AES symmetric key, the encrypted data structure containing the password hashes, and the initialization vector to Azure AD.
1. Azure AD stores the encrypted AES symmetric key, the encrypted data structure, and the initialization vector for the user.
1. Azure AD pushes the encrypted AES symmetric key, the encrypted data structure, and the initialization vector using an internal synchronization mechanism over an encrypted HTTP session to Azure AD Domain Services.
1. Azure AD Domain Services retrieves the private key for the tenant's instance from Azure Key vault.
1. For each encrypted set of data (representing a single user's password change), Azure AD Domain Services then performs the following steps:
    * Uses its private key to decrypt the AES symmetric key.
    * Uses the AES symmetric key with the initialization vector to decrypt the encrypted data structure that contains the password hashes.
    * Writes the Kerberos password hashes it receives to the Azure AD Domain Services domain controller. The hashes are saved into the user object's *supplementalCredentials* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.
    * Azure AD Domain Services writes the NTLM password hash it received to the Azure AD Domain Services domain controller. The hash is saved into the user object's *unicodePwd* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.

## <a name="enable-password-hash-synchronization"></a>암호 해시 동기화 사용

>[!IMPORTANT]
>AD FS(또는 기타 페더레이션 기술)에서 암호 해시 동기화로 마이그레이션하는 경우 [여기](https://aka.ms/adfstophsdpdownload)에 게시된 자세한 배포 가이드를 따르는 것이 좋습니다.

**기본 설정** 옵션을 사용하여 Azure AD Connect를 설치할 경우, 암호 해시 동기화를 사용하도록 자동으로 설정됩니다. 자세한 내용은 [기본 설정을 사용하여 Azure AD Connect 시작](how-to-connect-install-express.md)을 참조하세요.

Azure AD Connect를 설치할 때 사용자 지정 설정을 사용하는 경우 사용자 로그인 페이지에서 암호 해시 동기화를 사용할 수 있습니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](how-to-connect-install-custom.md)를 참조하세요.

![암호 해시 동기화 사용](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>암호 해시 동기화 및 FIPS
서버가 FIPS(Federal Information Processing Standard)에 따라 잠긴 다음 MD5가 비활성화됩니다.

**암호 해시 동기화에 MD5를 사용하려면 다음 단계를 수행합니다.**

1. %programfiles%\Azure AD Sync\Bin으로 이동합니다.
2. miiserver.exe.config를 엽니다.
3. 파일의 끝에서 구성/런타임 노드로 이동합니다.
4. 다음 노드를 추가 합니다. `<enforceFIPSPolicy enabled="false"/>`
5. 변경 내용을 저장합니다.

참조용으로 다음 조각처럼 보여야 합니다.

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

보안 및 FIPS에 대한 자세한 내용은 [Azure AD 암호 동기화, 암호화 및 FIPS 규정 준수](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)를 참조하세요.

## <a name="troubleshoot-password-hash-synchronization"></a>암호 해시 동기화 문제 해결
암호 해시 동기화에 문제가 있으면 [암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect 동기화: 사용자 지정 동기화 옵션](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
* [ADFS에서 암호 해시 동기화로 마이그레이션하기 위한 단계별 배포 계획 가져오기](https://aka.ms/authenticationDeploymentPlan)
