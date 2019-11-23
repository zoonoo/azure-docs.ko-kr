---
title: User migration approaches in Azure Active Directory B2C
description: Discusses both core and advanced concepts on user migration using the Azure AD Graph API, and optionally using Azure AD B2C custom policies.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9c01e22cfa96321994c16df6b61a52ebd4137549
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322927"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrate users to Azure Active Directory B2C

When you migrate your identity provider to Azure Active Directory B2C (Azure AD B2C), you might also need to migrate the user accounts. 이 문서에서는 기존 사용자 계정을 ID 공급자에서 Azure AD B2C로 마이그레이션하는 방법을 설명합니다. The article is not meant to be prescriptive, but rather, it describes a few scenarios. 개발자는 각 방식의 적합성에 대한 책임이 있습니다.

## <a name="user-migration-flows"></a>사용자 마이그레이션 흐름

With Azure AD B2C, you can migrate users through the [Azure AD Graph API][B2C-GraphQuickStart]. 사용자 마이그레이션 프로세스는 두 개의 흐름으로 나누어 집니다.

- **사전 마이그레이션**: 이 흐름은 사용자의 자격 증명(사용자 이름 및 암호)에 대한 명확한 액세스 권한이 있거나 자격 증명이 암호화되어 있지만 암호를 해독할 수 있는 경우에 적용됩니다. 사전 마이그레이션 프로세스에는 이전 ID 공급자의 사용자를 읽고 Azure AD B2C 디렉터리에 새 계정을 만드는 작업이 포함됩니다.

- **사전 마이그레이션 및 암호 재설정** - 이 흐름은 사용자의 암호에 액세스할 수 없을 경우에 적용됩니다. 다음은 그 예입니다.
  - 암호는 해시 형식으로 저장됩니다.
  - 암호는 액세스할 수 없는 ID 공급자에 저장됩니다. 이전 ID 공급자는 웹 서비스를 호출하여 사용자 자격 증명의 유효성을 검사합니다.

두 흐름에서 처음으로 사전 마이그레이션 프로세스를 실행하고, 이전 ID 공급자의 사용자를 읽고, Azure AD B2C 디렉터리에 새 계정을 만듭니다. If you don't have the password, you create the account by using a password that's generated randomly. 그런 다음 사용자에게 암호 변경을 요청하거나 사용자가 처음 로그인 할 때 Azure AD B2C에서 사용자에게 암호를 다시 설정하도록 요청합니다.

## <a name="password-policy"></a>암호 정책

(로컬 계정의) Azure AD B2C 암호 정책은 Azure AD 정책을 기반으로 합니다. The Azure AD B2C sign-up or sign-in and password reset policies use the "strong" password strength and don't expire any passwords. 자세한 내용은 [Azure AD 암호 정책][AD-PasswordPolicies]을 참조하세요.

마이그레이션하려는 계정이 [Azure AD B2C에 의해 적용되는 강력한 암호 강도][AD-PasswordPolicies]보다 약한 암호 강도를 사용하는 경우 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경하려면 `passwordPolicies` 속성을 `DisableStrongPassword`로 설정합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1단계: Azure AD Graph API를 사용하여 사용자 마이그레이션

(암호 또는 임의의 암호를 사용하여) Graph API를 통해 Azure AD B2C 사용자 계정을 만듭니다. 이 섹션에서는 Graph API를 사용하여 Azure AD B2C 디렉터리에서 사용자 계정을 만드는 프로세스를 설명합니다.

### <a name="step-11-register-your-application-in-your-tenant"></a>1\.1단계: 테넌트에서 애플리케이션 등록

Graph API와 통신하려면 먼저 관리자 권한이 있는 서비스 계정이 있어야 합니다. In Azure AD, you register an application and enable write access to the directory. The application credentials are the **Application ID** and **Application Secret**. 애플리케이션은 사용자로서가 아닌 자체로서 Graph API를 호출합니다.

First, register an application that you can use for management tasks like user migration.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Step 1.2: Grant administrative permission to your application

Next, grant the application the Azure AD Graph API permissions required for writing to the directory.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Step 1.3: Create the application secret

Create a client secret (key) for use by the user migration application that you configure in a later step.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Now you have an application with permissions to create, read, and update users in your Azure AD B2C tenant.

### <a name="step-14-optional-environment-cleanup"></a>1\.4단계: (선택 사항) 환경 정리

The *Read and write directory data* permission does *not* include the right to delete users. 애플리케이션에 사용자를 삭제할 수 있는 권한을 부여하려면(환경을 정리하기 위해) PowerShell을 실행하여 사용자 계정 관리자 권한을 설정해야 하는 추가 단계를 수행해야 합니다. 그렇지 않으면 다음 섹션으로 건너뛸 수 있습니다.

> [!IMPORTANT]
> B2C 테넌트에 대해 *로컬*인 B2C 테넌트 Administrator 계정을 사용해야 합니다. The account name syntax is *admin\@contosob2c.onmicrosoft.com*.

In this PowerShell script, which requires the [Azure AD PowerShell V2 module][AD-Powershell], do the following:

1. 온라인 서비스에 연결 이렇게 하려면 Windows PowerShell 명령 프롬프트에서 `Connect-AzureAD` cmdlet을 실행하고 자격 증명을 제공합니다.

1. **애플리케이션 ID**를 사용하여 애플리케이션에 사용자 계정 관리자 역할을 할당할 수 있습니다. 이러한 역할은 잘 알려진 식별자로서 스크립트에 **애플리케이션 ID**를 입력하기만 하면 됩니다.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

`$AppId` 값을 Azure AD **애플리케이션 ID**로 변경합니다.

## <a name="step-2-pre-migration-application-sample"></a>2단계: 사전 마이그레이션 애플리케이션 샘플

You can find the pre-migration code sample in the community-maintained `azure-ad-b2c/user-migration` GitHub repository:

[azure-ad-b2c/user-migration/pre-migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>2\.1단계: 마이그레이션 데이터 파일 편집

샘플 앱은 더미 사용자 데이터가 포함된 JSON 파일을 사용합니다. 샘플을 성공적으로 실행한 후에 자체 데이터베이스의 데이터를 사용하도록 코드를 변경합니다. 또는 사용자 프로필을 JSON 파일로 내보낸 다음 앱이 해당 파일을 사용하도록 설정합니다.

JSON 파일을 편집하려면 `AADB2C.UserMigration.sln` Visual Studio 솔루션을 엽니다. `AADB2C.UserMigration` 프로젝트에서 `UsersData.json` 파일을 엽니다.

![Portion of UsersData.json file showing JSON blocks of two users](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

보이는 것 처럼 이 파일에는 사용자 엔터티 목록이 포함됩니다. 각 사용자 엔터티에는 다음과 같은 속성이 있습니다.

- 이메일
- displayName
- firstname
- Lastname
- 암호(비워 둘 수 있음)

> [!NOTE]
> 컴파일 시 Visual Studio는 파일을 `bin` 디렉터리로 복사합니다.

### <a name="step-22-configure-the-application-settings"></a>2\.2단계: 애플리케이션 설정 구성

`AADB2C.UserMigration` 프로젝트에서 *App.config* 파일을 엽니다. 다음과 같은 앱 설정을 고유한 값으로 바꿉니다.

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Azure 테이블 연결 문자열을 사용하는 방법은 다음 섹션에 설명되어 있습니다.
> - B2C 테넌트 이름은 테넌트를 만들 때 입력한 도메인이며, Azure Portal에 표시됩니다. 일반적으로 테넌트 이름은 접미사 *.onmicrosoft.com*으로 끝납니다(예: *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>2\.3단계: 사전 마이그레이션 프로세스 실행

`AADB2C.UserMigration` 솔루션을 마우스 오른쪽 단추로 클릭한 다음 샘플을 다시 빌드합니다. 성공하는 경우 `UserMigration.exe` 실행 파일이 `AADB2C.UserMigration\bin\Debug\net461`에 있어야 합니다. 마이그레이션 프로세스를 실행하려면 다음 명령줄 매개 변수 중 하나를 사용합니다.

- **암호로 사용자를 마이그레이션**하려면 `UserMigration.exe 1` 명령을 사용합니다.

- **임의 암호로 사용자를 마이그레이션**하려면 `UserMigration.exe 2` 명령을 사용합니다. 또한 이 작업에서는 Azure 테이블 엔터티를 만듭니다. 나중에 서비스 REST API 서비스를 호출하도록 정책을 구성합니다. 서비스는 Azure 테이블을 사용하여 마이그레이션 프로세스를 추적하고 관리합니다.

![Command Prompt window showing output of UserMigration.exe command](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>2\.4단계: 사전 마이그레이션 프로세스 확인

마이그레이션의 유효성을 검사하려면 다음 두 가지 방법 중 하나를 사용합니다.

- 사용자를 표시 이름으로 검색하려면 Azure Portal을 사용합니다.

   1. Open **Azure AD B2C**, and then select **Users**.
   1. 검색 상자에 사용자의 표시 이름을 입력한 다음 사용자 프로필을 확인합니다.

- To retrieve a user by sign-in email address, use the sample application:

   1. 다음 명령 실행:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > 다음 명령을 사용하여 표시 이름으로 사용자를 검색할 수도 있습니다. `UserMigration.exe 4 "<Display name>"`

   1. JSON 편집기에서 UserProfile.json 파일을 열어 사용자의 정보를 봅니다.

      ![UserProfile.json file open in the Visual Studio Code editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>2\.5단계: (선택 사항) 환경 정리

Azure AD 테넌트를 정리하고 Azure AD 디렉터리에서 사용자를 제거하려면 `UserMigration.exe 5` 명령을 실행합니다.

> [!NOTE]
> * 테넌트를 정리하려면 애플리케이션에 대한 사용자 계정 관리자 권한을 구성합니다.
> * 샘플 마이그레이션 앱은 JSON 파일에 나열된 모든 사용자를 정리합니다.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>2\.6단계: 마이그레이션된 사용자로 로그인(암호 사용)

사용자 암호를 사용하여 사전 마이그레이션 프로세스를 실행하고 나면 계정을 사용할 준비가 되고 사용자는 Azure AD B2C를 사용하여 애플리케이션에 로그인할 수 있습니다. 사용자 암호에 액세스할 수 없는 경우 다음 섹션을 진행합니다.

## <a name="step-3-help-users-reset-their-password"></a>3단계: 사용자가 암호를 다시 설정하도록 돕기

임의의 암호로 사용자를 마이그레이션하는 경우 사용자는 자신의 암호를 다시 설정해야 합니다. 암호를 다시 설정하도록 도우려면 링크가 있는 환영 전자 메일을 보내서 암호를 재설정하도록 합니다.

To get the link to your password reset policy, follow these steps. This procedure assumes you've previously created a password reset [custom policy](active-directory-b2c-get-started-custom.md).

1. Select the directory containing your Azure AD B2C tenant by using the **Directory + subscription** filter in the upper-right section of the [Azure portal](https://portal.azure.com).
1. Select **Azure AD B2C** in the left-hand menu (or from within **All services**).
1. Under **Policies**, select **Identity Experience Framework**.
1. Select your password reset policy. For example, *B2C_1A_PasswordReset*.
1. Select your application in the **Select application** drop-down.

    > [!NOTE]
    > **Run now** requires at least one application to be registered in your tenant. To learn how to register applications, see [Tutorial: Register an application in Azure Active Directory B2C][B2C-AppRegister].

1. Copy the URL shown in the **Run now endpoint** text box, and then send it to your users.

    ![Password reset policy page with Run now endpoint highlighted](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4단계: (선택 사항) 정책을 변경하여 사용자 마이그레이션 상태 확인 및 설정

> [!NOTE]
> 사용자 마이그레이션 상태를 확인하고 변경하려면 사용자 지정 정책을 사용해야 합니다. The set-up instructions from [Get started with custom policies][B2C-GetStartedCustom] must be completed.

사용자가 먼저 암호를 재설정하지 않고 로그인하려고 하면 정책은 친절한 오류 메시지를 반환해야 합니다. 다음은 그 예입니다.

> *Your password has expired. To reset it, select the Reset Password link.*

이 선택적 단계에서는 [사용자 지정 정책 시작][B2C-GetStartedCustom] 문서에 설명된 대로 Azure AD B2C 사용자 지정 정책을 사용해야 합니다.

이 섹션에서는 로그인 시 사용자 마이그레이션 상태를 확인하도록 정책을 변경합니다. 사용자가 암호를 변경하지 않은 경우 사용자에게 **암호를 잊어 버렸나요?** 링크를 선택하라는 HTTP 409 오류 메시지를 반환합니다.

암호 변경을 추적하려면 Azure 테이블을 사용합니다. 명령줄 매개 변수 `2`로 사전 마이그레이션 프로세스를 실행할 때 Azure 테이블에 사용자 엔터티를 만듭니다. 서비스는 다음을 수행합니다.

- 로그인 시 Azure AD B2C 정책은 마이그레이션 RESTful 서비스를 호출하여 전자 메일 메시지를 입력 클레임으로 보냅니다. 서비스는 Azure 테이블에서 이메일 주소를 검색합니다. 주소가 존재하면 서비스는 *암호를 변경해야 합니다.* 라는 오류 메시지를 throw합니다.

- 사용자가 암호를 성공적으로 변경한 후에 해당 엔터티를 Azure 테이블에서 제거합니다.

> [!NOTE]
> Azure 테이블을 사용하여 이 샘플을 간소화합니다. Azure AD B2C 계정의 사용자 지정 속성으로 또는 원하는 데이터베이스에 마이그레이션 상태를 저장할 수 있습니다.

### <a name="41-update-your-application-setting"></a>4.1: 애플리케이션 설정 업데이트

1. RESTful API 데모를 테스트하려면 Visual Studio에서 `AADB2C.UserMigration.sln`을 엽니다.
1. In the `AADB2C.UserMigration.API` project, open the *Web.config* file. 설정을 [2.2단계](#step-22-configure-the-application-settings)에서 구성된 것으로 바꿉니다.

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>4\.2단계: Azure App Services에 웹 애플리케이션 배포

솔루션 탐색기에서 `AADB2C.UserMigration.API`를 마우스 오른쪽 단추로 클릭하고, "게시..."를 선택합니다. 지침을 따라 Azure App Service에 게시합니다. 자세한 내용은 [Azure App Service에 앱 배포][AppService-Deploy]를 참조하세요.

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>4\.3단계: 정책에 기술 프로필 및 기술 프로필 유효성 검사 추가

1. 솔루션 탐색기에서 "솔루션 항목"을 확장하고, *TrustFrameworkExtensions.xml* 정책 파일을 엽니다.
1. `yourtenant.onmicrosoft.com`에서 `TenantId`, `PublicPolicyUri` 및 `<TenantId>` 필드를 테넌트의 이름으로 변경합니다.
1. Under the `<TechnicalProfile Id="login-NonInteractive">` element, replace all instances of `ProxyIdentityExperienceFrameworkAppId` and `IdentityExperienceFrameworkAppId` with the Application IDs configured in [Getting started with custom policies][B2C-GetStartedCustom].
1. `<ClaimsProviders>` 노드에서 다음 XML 코드 조각을 찾습니다. `ServiceUrl` 값을 변경하여 Azure App Service URL을 가리킵니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

앞의 기술 프로필은 하나의 `signInName` 입력 클레임을 정의합니다(전자 메일로 보내기). 로그인 시 클레임은 RESTful 엔드포인트에 전송됩니다.

After you define the technical profile for your RESTful API, configure the existing `SelfAsserted-LocalAccountSignin-Email` technical profile to additionally call your REST API technical profile by overriding it within your *TrustFrameworkExtensions.xml* file:

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Then, change the `Id` of the `LocalAccountSignIn` technical profile to `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>4\.4단계: 테넌트에 정책 업로드

1. [Azure Portal][Portal]에서 [Azure AD B2C 테넌트의 컨텍스트][B2C-NavContext]로 전환한 다음 **Azure AD B2C**를 선택합니다.
1. **ID 경험 프레임워크**를 선택합니다.
1. **모든 정책**을 선택합니다.
1. **정책 업로드**를 선택합니다.
1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
1. *TrustFrameworkExtensions.xml* 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>4\.5단계: 지금 실행을 사용하여 사용자 지정 정책 테스트

1. Select **Azure AD B2C**, and then select **Identity Experience Framework**.
1. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 *B2C_1A_signup_signin*을 연 다음 **지금 실행**을 선택합니다.
1. Enter the credentials of one of the migrated users, and then select **Sign In**. REST API는 다음과 같은 오류 메시지를 throw합니다.

    ![Sign-in Sign-up page showing the change password error message](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>4\.6단계: (선택 사항) REST API 문제 해결

거의 실시간으로 로깅 정보를 볼 수 있고 모니터링할 수 있습니다.

1. RESTful 애플리케이션 설정 메뉴의 **모니터링에서**에서 **진단 로그**를 선택합니다.
1. **애플리케이션 로깅 사용(파일 시스템)** 을 **켜기**로 설정합니다.
1. **수준**을 **자세한 정보 표시**로 설정합니다.
1. **저장**을 선택합니다.

    ![Diagnostics logs configuration page in Azure portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. **설정** 메뉴에서 **로그 스트림**을 선택합니다.
1. RESTful API의 출력을 확인합니다.

> [!IMPORTANT]
> 진단 로그는 개발 및 테스트 중에만 사용합니다. RESTful API 출력은 프로덕션 환경에서 노출되지 않아야 하는 기밀 정보를 포함할 수 있습니다.

## <a name="optional-download-the-complete-policy-files"></a>(선택 사항) 완성 정책 파일 다운로드

After you complete the [Get started with custom policies][B2C-GetStartedCustom] walk-through, we recommend that you build your scenario by using your own custom policy files. For your reference, we have provided [sample policy files][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
