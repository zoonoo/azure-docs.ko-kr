---
title: 사용자 지정 이메일 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C 지원 응용 프로그램을 사용하기 위해 등록할 때 고객에게 전송된 확인 전자 메일을 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671642"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 전자 메일 확인

Azure Active Directory B2C(Azure AD B2C)에서 사용자 지정 전자 메일을 사용하여 응용 프로그램을 사용하도록 등록한 사용자에게 사용자 지정 전자 메일을 보냅니다. [DisplayControls(현재](display-controls.md) 미리 보기) 및 타사 전자 메일 공급자를 사용하여 고유한 전자 메일 템플릿 및 *보낸 사람:주소* 및 제목을 사용할 수 있을 뿐만 아니라 지역화 및 사용자 지정 일회용 암호(OTP) 설정을 지원할 수 있습니다.

사용자 지정 전자 메일 확인은 [SendGrid](https://sendgrid.com) 또는 [SparkPost,](https://sparkpost.com)사용자 지정 REST API 또는 HTTP 기반 전자 메일 공급자(사용자 포함)와 같은 타사 전자 메일 공급자를 사용해야 합니다. 이 문서에서는 SendGrid를 사용하는 솔루션 설정에 대해 설명합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기

아직 없는 경우 SendGrid 계정을 설정하여 시작합니다(Azure 고객은 매월 25,000개의 무료 이메일의 잠금을 해제할 수 있음). 설정 지침은 Azure를 사용하여 [SendGrid를](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) 사용하여 전자 메일을 보내는 방법의 [SendGrid](../sendgrid-dotnet-how-to-send-email.md)계정 만들기 섹션을 참조하십시오.

[SendGrid API 키를 만드는](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)섹션을 완료해야 합니다. 이후 단계에서 사용할 API 키를 기록합니다.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C 정책 키 만들기

다음으로 정책을 참조할 Azure AD B2C 정책 키에 SendGrid API 키를 저장합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `SendGridSecret`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀**에 입력합니다.
1. **키 사용**에서 `Signature`를 선택합니다.
1. **만들기**를 선택합니다.

## <a name="create-sendgrid-template"></a>SendGrid 템플릿 만들기

Azure AD B2C 정책 키에 저장된 SendGrid 계정 및 SendGrid API 키를 사용하여 SendGrid [동적 트랜잭션 템플릿을](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)만듭니다.

1. SendGrid 사이트에서 [트랜잭션 템플릿](https://sendgrid.com/dynamic_templates) 페이지를 열고 템플릿 **만들기를**선택합니다.
1. 와 같은 `Verification email` 고유한 템플릿 이름을 입력한 다음 **저장을**선택합니다.
1. 새 템플릿 편집을 시작하려면 **버전 추가를**선택합니다.
1. **코드 편집기에서** 를 선택한 다음 **을 계속합니다.**
1. HTML 편집기에서 HTML 템플릿을 따라 붙여 넣거나 직접 사용합니다. `{{otp}}` 및 `{{email}}` 매개 변수는 일회성 암호 값 및 사용자 전자 메일 주소로 동적으로 대체됩니다.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. 왼쪽의 **설정을** 확장하고 **전자 메일 제목에**대해 을 입력합니다. `{{subject}}`
1. **템플릿 저장을**선택합니다.
1. 역화살표를 선택하여 **트랜잭션 템플릿** 페이지로 돌아갑니다.
1. 이후 단계에서 사용하기 위해 만든 템플릿의 **ID를** 기록합니다. `d-989077fbba9746e89f3f6411f596fb96`)을 입력합니다. 클레임 변환을 추가할 때 이 [ID를 지정합니다.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C 클레임 유형 추가

정책에서 다음 클레임 형식을 `<ClaimsSchema>` 요소에 추가합니다. `<BuildingBlocks>`

이러한 클레임 유형은 OTP(일회용 암호) 코드를 사용하여 전자 메일 주소를 생성하고 확인하는 데 필요합니다.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>클레임 변환 추가

다음으로 SendGrid로 전송된 요청의 본문이 될 JSON 문자열 클레임을 출력하려면 클레임 변환이 필요합니다.

JSON 개체의 구조는 InputParameters의 점 표기법의 ID와 입력 클레임의 변환클레임 유형에 의해 정의됩니다. 점 표기의 숫자는 배열을 의미합니다. 값은 InputClaims의 값과 InputParameters의 "값" 속성에서 비롯됩니다. JSON 클레임 변환에 대한 자세한 내용은 [JSON 클레임 변환을](json-transformations.md)참조하십시오.

다음 클레임 변환을 `<ClaimsTransformations>` 요소에 `<BuildingBlocks>`추가합니다. 클레임 변환 XML에 대한 다음 업데이트를 수행합니다.

* SendGrid `template_id` 템플릿 만들기에서 이전에 만든 SendGrid 트랜잭션 템플릿의 ID로 [InputParameter](#create-sendgrid-template)값을 업데이트합니다.
* 주소 `from.email` 값을 업데이트합니다. 유효한 이메일 주소를 사용하여 확인 이메일이 스팸으로 표시되지 않도록 합니다.
* `personalizations.0.dynamic_template_data.subject` 조직에 적합한 제목 줄로 제목 줄 입력 매개 변수의 값을 업데이트합니다.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>DataUri 콘텐츠 정의 추가

에서 클레임 변환 `<BuildingBlocks>`아래 버전 2.0.0 데이터 URI를 참조하려면 다음 [ContentDefinition를](contentdefinitions.md) 추가합니다.

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>디스플레이 컨트롤 만들기

확인 표시 컨트롤은 사용자에게 전송된 확인 코드로 이메일 주소를 확인하는 데 사용됩니다.

이 예제 표시 컨트롤은 다음으로 구성됩니다.

1. 사용자로부터 `email` 주소 클레임 유형을 수집합니다.
1. 사용자가 `verificationCode` 사용자에게 전송된 코드와 함께 클레임 유형을 제공할 때까지 기다립니다.
1. 이 `email` 디스플레이 컨트롤에 대한 참조가 있는 자체 어설션된 기술 프로파일로 되돌아갑니다.
1. `SendCode` 작업을 사용하여 OTP 코드를 생성하고 OTP 코드가 있는 전자 메일을 사용자에게 보냅니다.

![확인 코드 이메일 작업 보내기](media/custom-email/display-control-verification-email-action-01.png)

콘텐츠 정의에서 여전히 `<BuildingBlocks>`내의 [표시 컨트롤](display-controls.md) 형식 [확인 제어를](display-control-verification.md) 정책에 추가합니다.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>OTP 기술 프로필 추가

기술 `GenerateOtp` 프로필은 이메일 주소에 대한 코드를 생성합니다. 기술 `VerifyOtp` 프로필은 이메일 주소와 연결된 코드를 확인합니다. 형식의 구성과 일회성 암호의 만료를 변경할 수 있습니다. OTP 기술 프로필에 대한 자세한 내용은 [일회성 암호 기술 프로필 정의를](one-time-password-technical-profile.md)참조하십시오.

`<ClaimsProviders>` 요소에 다음 기술 프로필을 추가합니다.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>REST API 기술 프로필 추가

이 REST API 기술 프로필은 SendGrid 형식을 사용하여 전자 메일 콘텐츠를 생성합니다. RESTful 기술 프로필에 대한 자세한 내용은 [RESTful 기술 프로필 정의를](restful-technical-profile.md)참조하십시오.

OTP 기술 프로파일과 마찬가지로 `<ClaimsProviders>` 요소에 다음 기술 프로필을 추가합니다.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>디스플레이 컨트롤에 대한 참조 만들기

마지막 단계에서 만든 DisplayControl에 참조를 추가합니다. 이전 버전의 Azure AD B2C 정책을 사용한 경우 기존 `LocalAccountSignUpWithLogonEmail` 자체 어설션기술 프로필을 다음으로 바꿉습니다. 이 기술 `DisplayClaims` 프로필은 DisplayControl을 참조하여 사용합니다.

자세한 내용은 [자체 어설션된 기술 프로필](restful-technical-profile.md) 및 디스플레이 [컨트롤](display-controls.md)을 참조하십시오.

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[선택 사항] 이메일 현지화

이메일을 지역화하려면 지역화된 문자열을 SendGrid 또는 이메일 공급자에게 보내야 합니다. 예를 들어 전자 메일 제목, 본문, 코드 메시지 또는 전자 메일의 서명을 지역화합니다. 이렇게 하려면 [GetLocalizedStrings 변환 변환을](string-transformations.md) 사용 하 여 지역화된 문자열을 클레임 형식에 복사할 수 있습니다. JSON `GenerateSendGridRequestBody` 페이로드를 생성하는 클레임 변환에서는 지역화된 문자열을 포함하는 입력 클레임을 사용합니다.

1. 정책에서 제목, 메시지, codeIntro 및 서명과 같은 문자열 클레임을 정의합니다.
1. [GetLocalizedStrings변환](string-transformations.md) 변환을 정의하여 지역화된 문자열 값을 1단계에서 클레임으로 대체합니다.
1. 다음 `GenerateSendGridRequestBody` XML 스니펫을 사용하여 클레임 변환을 변경합니다.
1. Azure AD B2C에 의해 지역화될 모든 문자열 대신 동적 매개 변수를 사용하도록 SendGrind 템플릿을 업데이트합니다.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>다음 단계

GitHub에서 사용자 지정 이메일 확인 정책의 예를 찾을 수 있습니다.

[사용자 지정 이메일 확인 - 디스플레이컨트롤](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

사용자 지정 REST API 또는 HTTP 기반 SMTP 전자 메일 공급자 사용에 대한 자세한 내용은 [Azure AD B2C 사용자 지정 정책에서 RESTful 기술 프로필 정의를](restful-technical-profile.md)참조하십시오.
