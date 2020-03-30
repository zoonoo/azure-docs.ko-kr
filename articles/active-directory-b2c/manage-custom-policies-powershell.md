---
title: PowerShell을 통해 사용자 지정 정책 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책의 프로그래밍 방식으로 관리를 위해 Azure Active Directory(Azure AD) PowerShell cmdlet을 사용합니다. PowerShell을 사용하여 사용자 지정 정책을 생성, 읽기, 업데이트 및 삭제합니다.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187409"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure PowerShell을 사용하는 Azure AD B2C 사용자 지정 정책 관리

Azure PowerShell은 Azure AD B2C 테넌트에서 명령줄 및 스크립트 기반 사용자 지정 정책 관리를 위한 여러 cmdlet을 제공합니다. Azure AD PowerShell 모듈을 사용하여 다음을 수행하려면 알아봅니다.

* Azure AD B2C 테넌트에 사용자 지정 정책 나열
* 테넌트에서 정책 다운로드
* 콘텐츠를 덮어쓰면서 기존 정책 업데이트
* Azure AD B2C 테넌트에 새 정책 업로드
* 테넌트에서 사용자 지정 정책 삭제

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)및 [B2C IEF 정책 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) 역할을 가진 디렉터리에서 사용자에 대한 자격 증명
* 테넌트에 업로드된 [사용자 지정 정책](custom-policy-get-started.md)
* [그래프 **미리 보기 모듈용** Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>B2C 테넌트에 PowerShell 세션 연결

Azure AD B2C 테넌트에서 사용자 지정 정책을 사용하려면 먼저 [Connect-AzureAD][Connect-AzureAD] 명령을 사용하여 PowerShell 세션을 테넌트에 연결해야 합니다.

Azure AD B2C 테넌트의 이름으로 대체하여 `{b2c-tenant-name}` 다음 명령을 실행합니다. 디렉터리에서 [B2C IEF 정책 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) 역할을 할당한 계정으로 로그인합니다.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

성공적인 로그인을 보여주는 예제 명령 출력:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>테넌트의 모든 사용자 지정 정책 나열

사용자 지정 정책을 검색하면 Azure AD B2C 관리자가 해당 작업에 비즈니스 논리를 검토, 관리 및 추가할 수 있습니다. [AzureADMSTrustFrameworkPolicy 받기][Get-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 Azure AD B2C 테넌트에서 사용자 지정 정책의 아이디 목록을 반환합니다.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

예제 명령 출력:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>정책 다운로드

정책 아이디 목록을 검토한 후 [Get-AzureADMSTrustFrameworkPolicy를][Get-AzureADMSTrustFrameworkPolicy] 사용하여 특정 정책을 대상으로 지정하여 해당 콘텐츠를 다운로드할 수 있습니다.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

이 예제에서는 ID *B2C_1A_signup_signin* 정책이 다운로드됩니다.

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

정책 콘텐츠를 로컬로 편집하려면 명령 출력을 `-OutputFilePath` 인수가 있는 파일에 파이프한 다음 즐겨찾는 편집기에서 파일을 엽니다.

예제 명령 으로 출력을 파일로 전송합니다.

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>기존 정책 업데이트

만들거나 다운로드한 정책 파일을 편집한 후 [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 업데이트된 정책을 Azure AD B2C에 게시할 수 있습니다.

Azure AD `Set-AzureADMSTrustFrameworkPolicy` B2C 테넌트에 이미 있는 정책의 ID로 명령을 실행하면 해당 정책의 내용이 덮어씁입니다.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

추가 예제는 [설정-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령 참조를 참조하십시오.

## <a name="upload-a-new-policy"></a>새 정책 업로드

프로덕션 환경에서 실행 중인 사용자 지정 정책을 변경하는 경우 대체 또는 A/B 테스트 시나리오에 대해 여러 버전의 정책을 게시할 수 있습니다. 또는 기존 정책의 복사본을 만들고 몇 가지 작은 변경 내용으로 수정한 다음 다른 응용 프로그램에서 사용할 새 정책으로 업로드할 수 있습니다.

새 [AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 새 정책을 업로드합니다.

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>사용자 지정 정책 삭제

깨끗한 작업 수명 주기를 유지하려면 사용하지 않는 사용자 지정 정책을 주기적으로 제거하는 것이 좋습니다. 예를 들어 새 정책 집합으로 마이그레이션을 수행하고 새 정책의 기능을 확인한 후 이전 정책 버전을 제거할 수 있습니다. 또한 사용자 지정 정책 집합을 게시하려고 시도하고 오류가 발생하는 경우 실패한 릴리스의 일부로 만든 정책을 제거하는 것이 합리적일 수 있습니다.

[제거-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] 제거 명령을 사용 하 여 테넌트에서 정책을 삭제 합니다.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

예제 명령:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>정책 업로드 문제 해결

새 사용자 지정 정책을 게시하거나 기존 정책을 업데이트하려고 하면 정책 파일 상속 체인의 부적절한 XML 서식 지정 및 오류로 인해 유효성 검사가 실패할 수 있습니다.

예를 들어 잘못된 XML이 포함된 콘텐츠로 정책을 업데이트하려는 시도는 다음과 같습니다(간결성을 위해 출력이 잘립니다).

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

사용자 지정 정책 문제 해결에 대한 자세한 내용은 [Azure AD B2C 사용자 지정 정책 및 ID 환경 프레임워크](active-directory-b2c-guide-troubleshooting-custom.md)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 CI/CD(지속적인 통합/CD) 파이프라인의 일부로 사용자 지정 정책을 배포하는 자세한 내용은 [Azure DevOps 파이프라인에서 사용자 지정 정책 배포를](deploy-custom-policies-devops.md)참조하십시오.

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
