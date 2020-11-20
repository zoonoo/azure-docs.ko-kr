---
title: PowerShell을 사용 하 여 사용자 지정 정책 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책을 프로그래밍 방식으로 관리 하려면 Azure Active Directory (Azure AD) PowerShell cmdlet을 사용 합니다. PowerShell을 사용 하 여 사용자 지정 정책을 만들고, 읽고, 업데이트 하 고, 삭제 합니다.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29db2214ddabeb6df2aa937c4ccd1f9938143969
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949448"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure AD B2C 사용자 지정 정책 관리

Azure PowerShell은 Azure AD B2C 테 넌 트에서 명령줄 및 스크립트 기반 사용자 지정 정책 관리를 위한 몇 가지 cmdlet을 제공 합니다. Azure AD PowerShell 모듈을 사용 하 여 다음을 수행 하는 방법을 알아봅니다.

* Azure AD B2C 테 넌 트의 사용자 지정 정책 나열
* 테 넌 트에서 정책 다운로드
* 콘텐츠를 덮어써서 기존 정책 업데이트
* Azure AD B2C 테 넌 트에 새 정책 업로드
* 테 넌 트에서 사용자 지정 정책 삭제

## <a name="prerequisites"></a>사전 요구 사항

* [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 역할을 사용 하 여 디렉터리의 사용자에 대 한 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md)및 자격 증명
* 테 넌 트에 업로드 된 [사용자 지정 정책](custom-policy-get-started.md)
* [Graph **preview 모듈인** Azure AD PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>B2C 테 넌 트에 PowerShell 세션 연결

Azure AD B2C 테 넌 트에서 사용자 지정 정책에 대 한 작업을 수행 하려면 먼저 [AzureAD][Connect-AzureAD] 명령을 사용 하 여 PowerShell 세션을 테 넌 트에 연결 해야 합니다.

`{b2c-tenant-name}`Azure AD B2C 테 넌 트의 이름으로 대체 하 여 다음 명령을 실행 합니다. 디렉터리에서 [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 역할이 할당 된 계정으로 로그인 합니다.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

성공적인 로그인을 보여 주는 예제 명령 출력:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>테 넌 트의 모든 사용자 지정 정책을 나열 합니다.

사용자 지정 정책을 검색 하면 Azure AD B2C 관리자가 비즈니스 논리를 검토, 관리 및 해당 작업에 추가할 수 있습니다. [AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 명령을 사용 하 여 Azure AD B2C 테 넌 트의 사용자 지정 정책 id 목록을 반환 합니다.

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

정책 Id의 목록을 검토 한 후에는 [AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 를 사용 하 여 특정 정책을 대상으로 지정 하 여 해당 콘텐츠를 다운로드할 수 있습니다.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

이 예에서는 ID가 *B2C_1A_signup_signin* 인 정책이 다운로드 됩니다.

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

정책 콘텐츠를 로컬로 편집 하려면 명령 출력을 인수를 사용 하는 파일로 파이프 한 `-OutputFilePath` 다음 원하는 편집기에서 파일을 엽니다.

파일에 출력을 보내는 예제 명령:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>기존 정책 업데이트

만들거나 다운로드 한 정책 파일을 편집한 후에는 [AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령을 사용 하 여 Azure AD B2C에 업데이트 된 정책을 게시할 수 있습니다.

`Set-AzureADMSTrustFrameworkPolicy`Azure AD B2C 테 넌 트에 이미 있는 정책의 ID를 사용 하 여 명령을 실행 하는 경우 해당 정책의 콘텐츠를 덮어씁니다.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

추가 예제는 [AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령 참조를 참조 하세요.

## <a name="upload-a-new-policy"></a>새 정책 업로드

프로덕션에서 실행 되는 사용자 지정 정책을 변경 하는 경우 대체 또는 A/B 테스트 시나리오에 대 한 여러 버전의 정책을 게시할 수 있습니다. 또는 기존 정책의 복사본을 만들고 몇 개의 작은 변경 내용을 수정한 다음 다른 응용 프로그램에서 사용 하기 위한 새 정책으로 업로드할 수 있습니다.

[AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] 명령을 사용 하 여 새 정책을 업로드 합니다.

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>사용자 지정 정책 삭제

정상적인 작업 수명 주기를 유지 하려면 사용 하지 않는 사용자 지정 정책을 정기적으로 제거 하는 것이 좋습니다. 예를 들어 새 정책 집합에 대 한 마이그레이션을 수행 하 고 새 정책의 기능을 확인 한 후 이전 정책 버전을 제거 하는 것이 좋습니다. 또한 사용자 지정 정책 집합을 게시 하 고 오류를 수신 하려는 경우 실패 한 릴리스의 일부로 생성 된 정책을 제거 하는 것이 적합할 수 있습니다.

[AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] 명령을 사용 하 여 테 넌 트에서 정책을 삭제 합니다.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

예제 명령:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>정책 업로드 문제 해결

새 사용자 지정 정책을 게시 하거나 기존 정책을 업데이트 하는 경우 정책 파일 상속 체인에서 잘못 된 XML 형식 지정 및 오류가 발생 하면 유효성 검사가 실패할 수 있습니다.

예를 들어 다음은 잘못 된 XML이 포함 된 콘텐츠를 사용 하 여 정책을 업데이트 하려고 시도 하는 경우입니다. 출력은 간단히 하기 위해 잘립니다.

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

사용자 지정 정책 문제 해결에 대 한 자세한 내용은 [사용자 지정 정책 및 Id 경험 프레임 워크 Azure AD B2C 문제 해결](./troubleshoot-custom-policies.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용 하 여 CI/CD (지속적인 통합/지속적인 업데이트) 파이프라인의 일부로 사용자 지정 정책을 배포 하는 방법에 대 한 자세한 내용은 [Azure DevOps 파이프라인에서 사용자 지정 정책 배포](deploy-custom-policies-devops.md)를 참조 하세요.

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy