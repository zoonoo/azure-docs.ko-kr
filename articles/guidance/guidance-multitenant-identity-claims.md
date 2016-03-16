<properties
   pageTitle="다중 테넌트 응용 프로그램에서 클레임 기반 ID 작업 | Microsoft Azure"
   description="발급자 유효성 검사 및 권한 부여에 클레임을 사용하는 방법"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 다중 테넌트 응용 프로그램에서 클레임 기반 ID 작업

이 문서는 [시리즈의 일부]입니다. 이 시리즈와 함께 제공되는 완전한 [응용 프로그램 예제]도 있습니다.

## Azure AD의 클레임

사용자가 로그인하면 Azure AD는 사용자에 대한 클레임 집합을 포함하는 ID 토큰을 보냅니다. 클레임은 단순한 정보 조각이며, 키/값 쌍으로 표현됩니다. 예: `email`=`bob@contoso.com`. 클레임에는 사용자를 인증하고 클레임을 만드는 엔터티인 발급자(이 경우, Azure AD)가 있습니다. 발급자를 신뢰하므로 클레임도 신뢰합니다. (반대로 발급자를 신뢰하지 않으면 클레임도 신뢰하지 않습니다.)

개요:

1.	사용자를 인증합니다.
2.	IDP는 클레임 집합을 보냅니다.
3.	앱은 클레임을 정규화 또는 보강(선택 사항)합니다
4.	앱은 권한 부여를 결정하기 위해 클레임을 사용합니다.

OpenID Connect에서 얻은 클레임 집합은 인증 요청의 [범위 매개 변수]에 의해 제어됩니다. 하지만 Azure AD는 OpenID Connect를 통해 제한된 클레임 집합을 발급합니다. [지원되는 토큰 및 클레임 유형]을 참조하세요. 사용자에 대한 자세한 정보를 원하는 경우 Azure AD Graph API를 사용해야 합니다.

다음은 앱에서 일반적으로 고려하는 AAD의 일부 클레임입니다.

ID 토큰에서 클레임 유형 |	설명
-----------------------|--------------
aud | 토큰을 발급한 사람입니다. 응용 프로그램의 클라이언트 ID가 됩니다. 일반적으로 미들웨어가 자동으로 유효성 검사를 수행하기 때문에 이 클레임에 대해 걱정할 필요가 없습니다. 예제: `"91464657-d17a-4327-91f3-2ed99386406f"`
groups | 사용자가 구성원인 AAD 그룹 목록입니다. 예제: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | OIDC 토큰의 [발급자]입니다. 예: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
name | 사용자의 표시 이름입니다. 예제: `"Alice A."`
oid | AAD에서 사용자에 대한 개체 식별자입니다. 이 값은 변경 불가능하고 사용자에 대한 재사용할 수 없는 식별자입니다. 전자 메일이 아닌 이 값을 사용자에 대한 고유 식별자로 사용합니다. 전자 메일 주소는 변경될 수 있습니다. 앱에서 Azure AD Graph API를 사용하는 경우 개체 ID는 프로필 정보를 쿼리하는 데 사용하는 값입니다. 예제: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles | 사용자에 대한 앱 역할 목록입니다. 예제: `["SurveyCreator"]`
tid | 테넌트 ID. 이 값은 Azure AD에서 테넌트에 대한 고유한 식별자입니다. 예제: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | 사람이 읽을 수 있는 사용자의 표시 이름입니다. 예제: `"alice@contoso.com"`
upn | 사용자 계정 이름. 예제: `"alice@contoso.com"`

이 표는 클레임 유형을 ID 토큰에 표시되는 대로 나열합니다. ASP.NET Core 1.0에서 OpenID Connect 미들웨어는 사용자 계정에 대한 클레임 컬렉션을 채울 때 일부 클레임 유형을 변환합니다.

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## 클레임 변환

인증 흐름 중에 IDP에서 가져온 클레임을 수정하려고 할 수 있습니다. ASP.NET Core 1.0에서는 OpenID Connect 미들웨어의 **AuthenticationValidated** 이벤트 내부에서 클레임 변환을 수행할 수 있습니다. ([인증 이벤트] 참조)

**AuthenticationValidated** 중에 추가한 모든 클레임은 세션 인증 쿠키에 저장됩니다. Azure AD로 다시 푸시되지 않습니다.

클레임 변환에 대한 몇 가지 예는 다음과 같습니다.

-	**클레임 정규화** 또는 사용자 간에 일관된 클레임 만들기. 여러 IDP에서 클레임을 가져오는 경우 특히 유용합니다. 유사한 정보에 대해 다른 클레임 유형을 사용할 수 있습니다. 예를 들어, Azure AD는 사용자의 전자 메일을 포함하는 "upn" 클레임을 보냅니다. 다른 IDP는 "email" 클레임을 보낼 수 있습니다. 다음 코드는 "upn" 클레임을 "email" 클레임으로 변환합니다.

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- 값이 없는 클레임에 대해 **기본 클레임 값**을 추가합니다. 예를 들어 사용자를 기본 역할에 할당합니다. 일부 경우 이렇게 하면 권한 부여 논리를 간소화할 수 있습니다.
- **사용자 지정 클레임 유형**을 사용자에 대한 응용 프로그램 관련 정보와 함께 추가합니다. 예를 들어 데이터베이스에 사용자에 대한 일부 정보를 저장할 수 있습니다. 이 정보와 함께 사용자 지정 클레임을 인증 티켓에 추가할 수 있습니다. 클레임이 쿠키에 저장되므로 로그인 세션당 1번 데이터베이스에서 가져와야 합니다. 반면, 쿠키를 지나치게 많이 만들고 싶지 않으므로 쿠키 크기와 데이터베이스 조회 간에 균형을 맞추어야 합니다.   

인증 흐름이 완료되면 클레임을 `HttpContext.User`에서 사용할 수 있습니다. 이때 클레임을 읽기 전용 컬렉션으로 다루어야 합니다. 예를 들어 권한 부여를 결정하는 데 사용합니다.

## 발급자 유효성 검사
OpenID Connect에서 발급자 클레임("iss")은 ID 토큰을 발급한 IDP를 식별합니다. OIDC 인증 흐름의 일부는 발급자 클레임이 실제 발급자와 일치하는지 확인합니다. OIDC 미들웨어가 이를 처리합니다.

Azure AD에서 발급자 값은 AD 테넌트당 고유합니다(`https://sts.windows.net/<tenantID>`). 따라서 응용 프로그램은 발급자가 앱에 로그인하도록 허용된 테넌트를 나타내는지 확인하기 위해 추가 검사를 수행해야 합니다.

단일 테넌트 응용 프로그램의 경우 발급자가 사용자 고유의 테넌트인지 확인할 수 있습니다. 실제로는 OIDC 미들웨어가 기본적으로 이 작업을 자동으로 수행합니다. 다중 테넌트 앱에서는 서로 다른 테넌트에 해당하는 여러 발급자를 허용해야 합니다. 다음은 사용되는 일반적인 방법입니다.

-	OIDC 미들웨어 옵션에서 **ValidateIssuer**를 false로 설정합니다. 자동 확인이 해제됩니다.
-	테넌트를 등록할 때 테넌트와 발급자를 사용자 DB에 저장합니다.
-	사용자가 로그인할 때마다 데이터베이스에서 발급자를 조회합니다. 발급자를 찾을 수 없으면 해당 테넌트가 등록되지 않은 것입니다. 등록 페이지로 리디렉션할 수 있습니다.
-  특정 테넌트를 블랙리스트에 올릴 수도 있습니다(예를 들어, 구독 요금을 지불하지 않은 고객).

자세한 내용은 [등록 및 테넌트 온보딩]을 참조하세요.

## 권한 부여에 클레임 사용

클레임이 있으면 사용자의 ID는 더 이상 모놀리식 엔터티가 아닙니다. 예를 들어 사용자는 전자 메일 주소, 전화 번호, 생일, 성별 등의 정보를 포함할 수 있습니다. 아마 사용자의 IDP는 이러한 모든 정보를 저장합니다. 하지만 사용자를 인증할 때 일반적으로 이러한 정보의 하위 집합을 클레임으로 가져옵니다. 이 모델에서는 사용자의 ID가 단순히 클레임의 번들입니다. 사용자에 대한 권한 부여를 결정할 때 특정 클레임 집합을 찾습니다. 즉, "사용자 X가 작업 Y를 수행할 수 있는가"라는 질문은 궁극적으로 "사용자 X가 클레임 Y를 포함하는가"입니다.

클레임을 확인하는 몇 가지 기본 패턴은 다음과 같습니다.

-  사용자에게 특정 값이 있는 특정 클레임이 있는지 확인하려면

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    이 코드는 사용자에게 "Admin" 값이 있는 역할 클레임이 있는지 확인합니다. 사용자에게 역할 클레임이 없거나 여러 역할 클레임이 있는 경우 적절하게 처리합니다.

    **ClaimTypes** 클래스는 일반적으로 사용되는 클레임 유형에 대한 상수를 정의합니다. 그러나 클레임 유형에 대해 임의의 문자열 값을 사용할 수 있습니다.

-	클레임 유형에 대해 단일 값을 가져오려면 최대 1개 값이 있을 것으로 예상되는 경우:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	클레임 유형에 대한 모든 값을 가져오려면:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

권한 부여에 클레임을 사용하는 방법에 대한 자세한 내용 [권한 부여]를 참조하세요.

## 추가 리소스

- [클레임 기반 권한 부여]


<!-- Links -->
[시리즈의 일부]: guidance-multitenant-identity.md
[범위 매개 변수]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[지원되는 토큰 및 클레임 유형]: ../active-directory/active-directory-token-and-claims.md
[발급자]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[인증 이벤트]: guidance-multitenant-identity-authenticate.md#authentication-events
[등록 및 테넌트 온보딩]: guidance-multitenant-identity-signup.md
[권한 부여]: guidance-multitenant-identity-authorize.md
[클레임 기반 권한 부여]: https://docs.asp.net/en/latest/security/authorization/claims.html
[응용 프로그램 예제]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->