---
title: 사용자 경험에서 REST API 클레임 교환 통합
titleSuffix: Azure AD B2C
description: 사용자 입력의 유효성 검사로 Azure AD B2C 사용자 경험에 REST API 클레임 교환을 통합 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3bea04ba077aebe9a52400a1292c5cd27c15b72e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950921"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>사용자 입력의 유효성 검사로 Azure AD B2C 사용자 경험에서 REST API 클레임 교환 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)의 기반이 되는 Id 경험 프레임 워크를 사용 하 여 사용자 경험에서 RESTful API와 통합할 수 있습니다. 이 연습에서는 Azure AD B2C .NET Framework RESTful services (웹 API)와 상호 작용 하는 방법에 대해 알아봅니다.

## <a name="introduction"></a>Introduction (Bevezetés)

Azure AD B2C를 사용 하 여 고유한 RESTful 서비스를 호출 하 여 사용자 경험에 고유한 비즈니스 논리를 추가할 수 있습니다. Id 경험 프레임 워크는 *입력 클레임* 컬렉션의 RESTful 서비스에 데이터를 보내고 *출력 클레임* 컬렉션의 RESTful에서 다시 데이터를 받습니다. RESTful service 통합을 사용 하면 다음을 수행할 수 있습니다.

* **사용자 입력 데이터의 유효성 검사**:이 작업은 잘못 된 데이터를 Azure AD에 유지 하지 못하도록 합니다. 사용자의 값이 유효 하지 않으면 RESTful 서비스는 사용자에 게 항목을 제공 하도록 지시 하는 오류 메시지를 반환 합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객의 데이터베이스에 있는지 확인할 수 있습니다.
* **입력 클레임 덮어쓰기**: 예를 들어 사용자가 모든 소문자 또는 모든 대문자에 이름을 입력 하면 이름 앞에 대문자를 사용 하 여 이름을 지정할 수 있습니다.
* **회사 lob (기간 업무) 응용 프로그램과의 통합을 통해 사용자 데이터 보강**: RESTful 서비스는 사용자의 전자 메일 주소를 수신 하 고, 고객의 데이터베이스를 쿼리하고, 사용자의 충성도 숫자를 Azure AD B2C으로 되돌릴 수 있습니다. 반환 클레임은 사용자의 Azure AD 계정에 저장 하거나, 다음 *오케스트레이션 단계*에서 평가 하거나, 액세스 토큰에 포함할 수 있습니다.
* **사용자 지정 비즈니스 논리 실행**: 푸시 알림을 보내고, 회사 데이터베이스를 업데이트 하 고, 사용자 마이그레이션 프로세스를 실행 하 고, 사용 권한을 관리 하 고, 데이터베이스를 감사 하 고, 기타 작업을 수행할 수 있습니다.

다음과 같은 방법으로 RESTful 서비스와의 통합을 디자인할 수 있습니다.

* **유효성 검사 기술 프로필**: RESTful 서비스에 대 한 호출은 지정 된 기술 프로필의 유효성 검사 기술 프로필 내에서 발생 합니다. 유효성 검사 기술 프로필은 사용자의 이동이 진행 되기 전에 사용자가 제공한 데이터의 유효성을 검사 합니다. 유효성 검사 기술 프로필을 사용 하 여 다음을 수행할 수 있습니다.
  * 입력 클레임을 보냅니다.
  * 입력 클레임의 유효성을 검사 하 고 사용자 지정 오류 메시지를 throw 합니다.
  * 출력 클레임을 다시 보냅니다.

* **클레임 교환**:이 디자인은 유효성 검사 기술 프로필과 유사 하지만 오케스트레이션 단계 내에서 발생 합니다. 이 정의는 다음으로 제한 됩니다.
  * 입력 클레임을 보냅니다.
  * 출력 클레임을 다시 보냅니다.

## <a name="restful-walkthrough"></a>RESTful 연습

이 연습에서는 사용자 입력의 유효성을 검사 하 고 사용자의 충성도를 제공 하는 .NET Framework web API를 개발 합니다. 예를 들어 응용 프로그램은 충성도 번호를 기준으로 *플래티넘 혜택* 에 대 한 액세스 권한을 부여할 수 있습니다.

Áttekintés:

* RESTful 서비스 (.NET Framework web API) 개발
* 사용자 경험에서 RESTful 서비스 사용
* 입력 클레임을 보내고 코드에서 읽습니다.
* 사용자의 이름을 확인 합니다.
* 충성도 번호를 다시 보냅니다.
* JSON Web Token (JWT)에 충성도 번호를 추가 합니다.

## <a name="prerequisites"></a>Előfeltételek

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료 합니다.

## <a name="step-1-create-an-aspnet-web-api"></a>1 단계: ASP.NET 웹 API 만들기

1. Visual Studio에서 **파일** > **새** > **프로젝트**를 선택 하 여 프로젝트를 만듭니다.
1. **새 프로젝트** 창에서 **Visual C#**  > **web** > **ASP.NET 웹 응용 프로그램 (.NET Framework)** 을 선택 합니다.
1. **이름** 상자에 응용 프로그램 이름 (예: *contoso.aadb2c.api*)을 입력 한 다음 **확인**을 선택 합니다.

    ![Visual Studio에서 새 Visual Studio 프로젝트 만들기](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

1. **New ASP.NET 웹 응용 프로그램** 창에서 **Web API** 또는 **Azure api 앱** 템플릿을 선택 합니다.

    ![Visual Studio에서 web API 템플릿 선택](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. 인증이 **인증 안 함**으로 설정 되어 있는지 확인 합니다.
1. A projekt létrehozásához válassza az **OK** lehetőséget.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2 단계: REST API 끝점 준비

### <a name="step-21-add-data-models"></a>2\.1 단계: 데이터 모델 추가

모델은 RESTful 서비스의 입력 클레임 및 출력 클레임 데이터를 나타냅니다. 코드에서는 입력 클레임 모델을 JSON 문자열에서 C# 개체 (모델)로 deserialize 하 여 입력 데이터를 읽습니다. ASP.NET web API는 자동으로 출력 클레임 모델을 JSON으로 deserialize 한 다음 직렬화 된 데이터를 HTTP 응답 메시지의 본문에 씁니다.

다음을 수행 하 여 입력 클레임을 나타내는 모델을 만듭니다.

1. 솔루션 탐색기 아직 열려 있지 않으면 **보기** > **솔루션 탐색기**를 선택 합니다.
1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Models** (Modellek) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra.

    ![Visual Studio에서 선택한 클래스 추가 메뉴 항목](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

1. 클래스 이름을 `InputClaimsModel`로 지정한 후 `InputClaimsModel` 클래스에 다음 속성을 추가 합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

1. `OutputClaimsModel`새 모델을 만들고 `OutputClaimsModel` 클래스에 다음 속성을 추가 합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. 입력 유효성 검사 오류 메시지를 throw 하는 데 사용 하는 `B2CResponseContent`모델을 하나 더 만듭니다. `B2CResponseContent` 클래스에 다음 속성을 추가 하 고, 누락 된 참조를 제공한 다음, 파일을 저장 합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>2\.2 단계: 컨트롤러 추가

Web API에서 _컨트롤러_ 는 HTTP 요청을 처리 하는 개체입니다. 컨트롤러가 출력 클레임을 반환 하거나, 이름이 유효 하지 않은 경우 충돌 HTTP 오류 메시지를 throw 합니다.

1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.

    ![Visual Studio에서 새 컨트롤러 추가](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. **스 캐 폴드 추가** 창에서 **Web API 컨트롤러-비어 있음**을 선택 하 고 **추가**를 선택 합니다.

    ![Visual Studio에서 비어 있는 웹 API 2 컨트롤러 선택](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. **컨트롤러 추가** 창에서 컨트롤러 이름을 **IdentityController**로 지정한 다음 **추가**를 선택 합니다.

    ![Visual Studio에서 컨트롤러 이름 입력](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    스 캐 폴딩은 *Controllers* 폴더에 *IdentityController.cs* 라는 파일을 만듭니다.

1. *IdentityController.cs* 파일이 아직 열려 있지 않으면 파일을 두 번 클릭 한 다음 파일의 코드를 다음 코드로 바꿉니다.

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>3 단계: Azure에 프로젝트 게시

1. 솔루션 탐색기에서 **contoso.aadb2c.api** 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **게시**를 선택 합니다.

    ![Visual Studio를 사용 하 여 Microsoft Azure App Service에 게시](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. **게시** 창에서 **Microsoft Azure App Service**를 선택 하 고 **게시**를 선택 합니다.

    ![Visual Studio를 사용 하 여 새 Microsoft Azure App Service 만들기](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **App Service 만들기** 창이 열립니다. Azure에서 ASP.NET 웹 앱을 실행 하는 데 필요한 모든 Azure 리소스를 만듭니다.

    > [!TIP]
    > 게시 하는 방법에 대 한 자세한 내용은 [Azure에서 ASP.NET 웹 앱 만들기](../app-service/app-service-web-get-started-dotnet-framework.md)를 참조 하세요.

1. **웹 앱 이름** 상자에 고유한 앱 이름을 입력 합니다 (유효한 문자는 a-z, 0-9 및 하이픈 (-)). 웹 앱의 URL은 http://< app_name >입니다. 여기서 *app_name* 은 웹 앱의 이름입니다. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

    ![App Service 속성 구성](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Azure 리소스 만들기를 시작 하려면 **만들기**를 선택 합니다.
    ASP.NET 웹 앱을 만든 후 마법사는 Azure에 게시 한 다음 기본 브라우저에서 앱을 시작 합니다.

1. 웹 앱의 URL을 복사 합니다.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4 단계: Trustframeworkextensions.xml 파일의 스키마에 새 `loyaltyNumber` 클레임 추가

`loyaltyNumber` 클레임은 아직 스키마에 정의 되어 있지 않습니다. *Trustframeworkextensions.xml* 파일의 시작 부분에서 찾을 수 있는 `<BuildingBlocks>` 요소 내에 정의를 추가 합니다.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>5 단계: 클레임 공급자 추가

모든 클레임 공급자에는 클레임 공급자와 통신 하는 데 필요한 끝점과 프로토콜을 결정 하는 하나 이상의 기술 프로필이 있어야 합니다.

클레임 공급자는 여러 가지 이유로 여러 기술 프로필을 가질 수 있습니다. 예를 들어 클레임 공급자가 여러 프로토콜을 지원 하거나, 끝점에서 다양 한 기능을 사용할 수 있거나, 릴리스는 다양 한 보증 수준을 가진 클레임을 포함할 수 있기 때문에 여러 기술 프로필을 정의할 수 있습니다. 하나의 사용자 경험에서 중요 한 클레임을 해제 하는 것이 허용 될 수 있습니다.

다음 XML 코드 조각에는 두 가지 기술 프로필을 사용 하는 클레임 공급자 노드가 포함 되어 있습니다.

* **TechnicalProfile Id = "REST-API-등록"** : RESTful 서비스를 정의 합니다.
  * `Proprietary`는 RESTful 기반 공급자의 프로토콜로 설명 되어 있습니다.
  * `InputClaims` Azure AD B2C에서 REST 서비스로 전송 되는 클레임을 정의 합니다.

    이 예제에서 `givenName` 클레임의 콘텐츠는 `firstName`으로 REST 서비스에 보내고, `surname` 클레임의 콘텐츠는 `lastName`로 REST 서비스에 전송 하 고, `email` 그대로 보냅니다. `OutputClaims` 요소는 RESTful 서비스에서 Azure AD B2C로 다시 검색 되는 클레임을 정의 합니다.

* **TechnicalProfile Id = "LocalAccountSignUpWithLogonEmail"** : 기존 기술 프로필 (기본 정책에 정의 됨)에 유효성 검사 기술 프로필을 추가 합니다. 등록 과정에서 유효성 검사 기술 프로필은 이전 기술 프로필을 호출 합니다. RESTful 서비스가 HTTP 오류 409 (충돌 오류)를 반환 하면 오류 메시지가 사용자에 게 표시 됩니다.

`<ClaimsProviders>` 노드를 찾은 후 `<ClaimsProviders>` 노드 아래에 다음 XML 코드 조각을 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

`AuthenticationType` 위의 설명 및 `AllowInsecureAuthInProduction` 프로덕션 환경으로 이동할 때 수행 해야 하는 변경 내용을 지정 합니다. 프로덕션을 위해 RESTful Api를 보호 하는 방법을 알아보려면 [기본 인증을 사용 하는 RESTful Api 보안](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) 및 [인증서 인증을 사용 하는 보안 RESTful api](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)를 참조 하세요.

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6 단계: 클레임을 응용 프로그램에 보내도록 신뢰 당사자 정책 파일에 `loyaltyNumber` 클레임 추가

*SignuTechnicalProfile* 의 RP (신뢰 당사자) 파일을 편집 하 고 Id = "policyprofile" 요소를 수정 하 여 다음을 추가 합니다. `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

새 클레임을 추가한 후 신뢰 당사자 코드는 다음과 같습니다.

```xml
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
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7 단계: 테 넌 트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테 넌 트의 컨텍스트로](active-directory-b2c-navigate-to-b2c-context.md)전환 하 고 **Azure AD B2C**를 엽니다.

1. **Id 경험 프레임 워크**를 선택 합니다.

1. **모든 정책**을 엽니다.

1. **정책 업로드**를 선택 합니다.

1. 정책이 있는 **경우 덮어쓰기** 확인란을 선택 합니다.

1. Trustframeworkextensions.xml 파일을 업로드 하 고 유효성 검사를 통과 하는지 확인 합니다.

1. Signu.xml 파일을 사용 하 여 이전 단계를 반복 합니다.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8 단계: 지금 실행을 사용 하 여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 선택한 다음 **Id 경험 프레임 워크**로 이동 합니다.

    > [!NOTE]
    > **이제를 실행** 하려면 테 넌 트에서 하나 이상의 응용 프로그램을 넌 합니다. 응용 프로그램을 등록 하는 방법에 대 한 자세한 내용은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조 하세요.

2. 업로드 한 **B2C_1A_signup_signin**, RP (신뢰 당사자) 사용자 지정 정책을 연 다음 **지금 실행**을 선택 합니다.

    ![Azure Portal의 B2C_1A_signup_signin 사용자 지정 정책 페이지](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. **지정 된 이름** 상자에 **test** 를 입력 하 여 프로세스를 테스트 합니다.
    Azure AD B2C 창 맨 위에 오류 메시지를 표시 합니다.

    ![등록 로그인 페이지에서 지정 된 이름 입력 유효성 검사 테스트](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. **지정 된 이름** 상자에 "Test" 이외의 이름을 입력 합니다.
    Azure AD B2C 사용자를 등록 한 다음 응용 프로그램에 loyaltyNumber를 보냅니다. 이 JWT의 번호를 적어둡니다.

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>필드 전체 정책 파일 및 코드 다운로드

* [사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 연습을 완료 한 후에는 고유한 사용자 지정 정책 파일을 사용 하 여 시나리오를 빌드하는 것이 좋습니다. 참조를 위해 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)을 제공 합니다.

* [참조를 위해 샘플 Visual Studio 솔루션](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)에서 전체 코드를 다운로드할 수 있습니다.

## <a name="next-steps"></a>Következő lépések

다음 작업은 기본 또는 클라이언트 인증서 인증을 사용 하 여 RESTful API를 보호 하는 것입니다. Api를 보호 하는 방법을 알아보려면 다음 문서를 참조 하세요.

* [기본 인증 (사용자 이름 및 암호)을 사용 하 여 RESTful API 보호](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [클라이언트 인증서를 사용 하 여 RESTful API 보호](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

RESTful 기술 프로필에서 사용할 수 있는 모든 요소에 대 한 자세한 내용은 [참조: RESTful 기술 프로필](restful-technical-profile.md)을 참조 하세요.
