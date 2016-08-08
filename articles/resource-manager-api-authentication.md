<properties 
   pageTitle="리소스 관리자 API를 사용한 권한 부여 | Microsoft Azure"
   description="앱을 Azure와 통합하기 위한 Azure Resource Manager API 및 Active Directory를 사용한 권한 부여 개발자 가이드"
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/12/2016"
   ms.author="dugill;tomfitz" />


# Azure Resource Manager API를 사용한 권한 부여 개발자 가이드

Azure를 사용하여 앱을 통합하거나 고객의 Azure 리소스를 관리하고자 하는 소프트웨어 개발자의 경우 이 토픽은 Azure Resource Manager API를 사용하여 인증하는 방법을 보여 줍니다.

앱에서 두 가지 방법으로 Resource Manager API에 액세스할 수 있습니다.

1. **사용자 + 앱 액세스**: 로그인한 사용자를 대신하여 리소스에 액세스하는 앱의 경우 이 방법을 사용합니다. 이 방법은 웹앱 및 명령줄 도구 등 Azure 리소스의 "대화형 관리"만 처리하는 앱에만 적용됩니다.
1. **앱만 액세스**: 리소스에 대한 직접 액세스 권한을 앱의 ID에 부여해야 하는 경우 이 방법을 사용합니다. 이 방법은 Azure에 대한 장기적인 "오프라인 액세스"가 필요한 디먼 서비스 및 예약된 작업에 적용됩니다.

이 토픽에서는 이러한 권한 부여 방법을 모두 채택하는 앱을 만드는 방법을 단계별로 설명합니다.

다음과 같은 기능을 가진 웹 응용 프로그램을 만듭니다.

1. Azure 사용자 로그인
2. 사용자가 소유한 Azure 구독의 목록을 가져오기 위해 사용자를 대신하여(사용자 + 앱 액세스) Resource Manager에 쿼리
3. 사용자가 구독을 앱에 "연결"하고, 그에 따라 구독에 대한 직접 액세스 권한을 응용 프로그램에 부여할 수 있도록 함
4. 오프라인 작업을 수행하기 위해 Resource Manager에 응용 프로그램으로 액세스(앱만 액세스)

다음은 작성해 볼 웹 응용 프로그램의 종단 간 흐름입니다.

![ARM Auth - 앱 등록 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

이 토픽의 모든 코드는 [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense)에서 실행해 볼 수 있는 웹앱으로 실행됩니다.

사용자로서 로그인하기 위해 사용할 계정의 유형을 선택합니다.

![로그인 선택](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

자격 증명을 제공 합니다.

![자격 증명 제공](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Azure 구독에 대한 앱 액세스 권한을 부여합니다.
 
 ![액세스 권한 부여](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
모니터링을 위해 구독을 앱에 연결합니다.

![구독 연결](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

앱에 대한 연결을 끊거나 복구합니다.

![구독 연결 끊기](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Azure Active Directory에 응용 프로그램 등록

Azure Active Directory(AD)를 사용하여 웹앱을 등록하는 작업으로 시작합니다. 앱 등록은 Azure AD의 사용자 앱에 대한 중앙 ID를 만듭니다. 이는 응용 프로그램이 Azure Resource Manager API를 인증하고 액세스하는 데 사용하는 OAuth 클라이언트 ID, 회신 URL 및 자격 증명 등 응용 프로그램에 관한 기본 정보를 저장합니다. 또한 앱 등록은 응용 프로그램이 사용자를 대신하여 Microsoft API에 액세스할 때 필요한 여러 가지 위임된 권한을 기록합니다.

[포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](resource-group-create-service-principal-portal.md) 토픽은 응용 프로그램을 설치하는 데 필요한 모든 단계를 보여 줍니다. 다음과 같은 속성을 가진 응용 프로그램을 만들 때 해당 토픽을 참조하세요.

- **CloudSense**라는 이름의 웹 응용 프로그램
- 로그인 URL 및 **http://{domain_name_of_your_directory}/{name_of_the_app}** 형식의 앱 ID URI
- 응용 프로그램에 서명하기 위한 인증 키
- **Azure Service Management API**에 대해 위임된 권한 **Azure Service Management 액세스**. **Azure Active Directory**에 대한 기본값 **Single Sign-On 사용 및 사용자 프로필 읽기**를 그대로 둡니다.
- 다중 테넌트 응용 프로그램

### 선택적 구성 - 인증서 자격 증명

또한 Azure AD는 응용 프로그램에 대한 인증서 자격 증명을 지원합니다. 즉, 자체 서명된 인증서를 만들고 개인 키를 유지하고 Azure AD 응용 프로그램 등록에 공개 키를 추가합니다. 인증을 위해 응용 프로그램이 개인 키를 사용하여서명한 Azure AD에 작은 페이로드를 보내면 Azure AD가 등록된 공개 키를 사용하여 서명의 유효성을 검사합니다.

인증서 구성에 대한 자세한 내용은 [Office 365에서 서비스 및 디먼 앱 만들기](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365)를 참조하세요. "응용 프로그램에 대한 X.509 공개 인증서 구성" 섹션에 인증서를 설정하는 단계별 지침이 있습니다. 또는 [Azure Resource Manager를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md)에서 Azure PowerShell 또는 Azure CLI를 통해 인증서를 구성하는 예제를 참조하세요.

## 사용자를 인증하고 액세스 토큰 가져오기

이제 응용 프로그램 코딩을 시작하는 데 필요한 모든 것을 갖추게 되었습니다. 이 토픽은 종단 간 흐름의 각 단계에 대한 REST API 예제 및 각 단계에 관련된 C# 코드에 대한 링크를 제공합니다. 전체 ASP.NET MVC 응용 프로그램 샘플은 [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense)에서 사용할 수 있습니다.

사용자가 응용 프로그램에 자신의 Azure 구독을 연결하기 위해 결정하는 지점에서 시작합니다.

사용자에게 다음 두 가지를 요구해야 합니다.

1. **디렉터리 도메인 이름**: 사용자의 Azure 구독과 연결된 Azure Active Directory의 도메인 이름입니다. 이 Azure AD에 OAuth 2.0 권한 부여 요청을 전송해야 합니다. 사용자는 Azure 포털로 이동하고 오른쪽 위에서 계정을 선택하여 Azure AD의 도메인 이름을 알아낼 수 있습니다. 사용자에게 다음과 같은 시각적 지침을 제공할 수 있습니다.

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Microsoft 계정 대 회사 계정**: 사용자가 자신의 Azure 구독을 관리하기 위해 Microsoft 계정(일명 Live Id)을 사용하는지 아니면 회사 계정(일명 조직 계정)을 사용하는지 여하를 결정합니다. Microsoft 계정을 사용하는 경우 응용 프로그램은 Azure AD에 대해 사용자를 Microsoft 계정 로그인 페이지로 직접 이동하도록 지시하는 쿼리 문자열 매개 변수(&domain\_hint=live.com)를 사용하여 사용자를 Azure Active Directory 로그인 페이지로 리디렉션합니다. 둘 중 한 유형의 계정에 대해 받는 권한 부여 코드와 토큰은 동일한 방법으로 처리됩니다.

그러면 응용 프로그램이 OAuth 2.0 권한 부여 요청을 사용하여 사용자를 Azure AD로 리디렉션하여 사용자의 자격 증명을 인증하고 권한 부여 코드를 돌려 줍니다. 응용 프로그램은 권한 부여 코드를 사용하여 Resource Manager에 대한 액세스 토큰을 가져옵니다.

### 인증 요청(OAuth 2.0)

Azure AD 권한 부여 끝점에 대한 Open ID Connect/OAuth2.0 권한 부여 요청을 실행합니다.

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [권한 부여 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx) 토픽에서 설명합니다.

다음 예제에서는 OAuth2.0 권한 부여를 요청하는 방법을 보여 줍니다.

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD는 사용자를 인증하고 필요한 경우 사용자에게 앱 사용 권한 부여를 요청합니다. 그러면 응용 프로그램의 회신 URL에 인증 코드가 반환됩니다. 요청한 response\_mode에 따라 Azure AD는 쿼리 문자열에 데이터를 다시 보내거나 데이터를 게시합니다.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### 인증 요청(Open ID Connect)

사용자를 대신하여 Azure Resource Manager에 액세스할 뿐만 아니라 사용자가 자신의 Azure AD 계정을 사용하여 응용 프로그램에 로그인할 수 있도록 하려면 Open ID Connect 권한 부여 요청을 실행합니다. 또한 Open ID Connect를 사용하면 응용 프로그램이 Azure AD로부터 앱에서 사용자를 로그인하는 데 사용할 수 있는 id\_token을 받습니다.

OAuth2.0 권한 부여 요청 쿼리 문자열 매개 변수는 다음과 같습니다.

| QS 매개 변수 | 값
|----|----
| client\_id | 응용 프로그램의 클라이언트 ID
| response\_mode | **form\_post** 또는 **query**
| response\_type | **code+id\_token**
| redirect\_uri | 응용 프로그램의 URL 인코딩된 회신 URL 예: http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Azure 서비스 관리 API의 URL 인코딩된 ID: https://management.core.windows.net/ |
| scope | openid+profile
| nonce | 권한 부여 요청을 반환된 id\_token에 연결하여 권한 부여 응답을 요청하고 재생되지 않도록 하는 데이터 부분입니다.
| domain\_hint | live.com <br />**참고**: 사용자가 Microsoft 계정을 사용하여 자신의 Azure 구독을 관리하는 경우 domain\_hint 매개 변수만 사용합니다.
| state | 필요에 따라 Azure AD가 응답과 함께 다시 반환하게 하려는 상태 데이터를 지정합니다.

Open ID Connect 요청 예제:

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD는 사용자를 인증하고 필요한 경우 사용자에게 앱 사용 권한 부여를 요청합니다. 그러면 응용 프로그램의 회신 URL에 인증 코드가 반환됩니다. 요청한 response\_mode에 따라 Azure AD는 쿼리 문자열에 데이터를 다시 보내거나 데이터를 게시합니다.

Open ID Connect 응답 예제:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### id\_token 유효성 검사

응용 프로그램이 사용자를 로그인하기 전에 id\_token의 유효성을 검사해야 합니다. 토큰 유효성 검사는 관련 토픽이며 개발 플랫폼에 표준 JSON 웹 토큰 처리기 라이브러리를 사용하는 것이 좋습니다([.NET Azure AD JWT 처리기 소스 코드](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs) 참조). 즉, 응용 프로그램의 보안은 사용자의 책임이므로 id\_token을 올바르게 처리하기 위해 사용하는 라이브러리가 토큰의 다음과 같은 측면에 대한 유효성을 검사하도록 확인합니다.

- **토큰의 타이밍**: nbf와 exp 클레임을 검사하여 토큰이 너무 새롭거나 너무 오래되지 않았는지 확인합니다. 시간 오차에 대비하기 위해 약간의 slack(5분)을 유지하는 것이 관례입니다.
- **발급자**: iss 클레임을 검사하여 토큰의 발급자가 Azure Active Directory인지 확인: https://sts.windows.net/{tenant_id_of_the_directory}
- **대상 그룹**: aud 클레임을 검사하여 응용 프로그램에 대한 토큰이 만들어졌는지 확인합니다. 값은 응용 프로그램의 클라이언트 ID여야 합니다.
- **Nonce**: 권한 부여 요청에 보낸 nonce 데이터를 기준으로 nonce 클레임을 검사하여 응답을 응용 프로그램에서 요청했는지 그리고 토큰이 재생되지 않는지 확인합니다.
- **서명**: 앱은 Azure Active Directory에서 토큰에 서명했는지 확인해야 합니다. Azure AD 서명 키는 자주 순환되므로 앱은 매일 새로 고친 키를 폴링하거나 서명 유효성 검사가 실패한 경우 새로 고친 키를 취소해야 합니다. 자세한 내용은 [Azure AD의 서명 키 롤오버에 대한 중요한 정보](active-directory/active-directory-signing-key-rollover.md)를 참조하세요.

**id\_token**의 유효성 검사를 실행한 후 oid 클레임 값을 변경 불가능하고 재사용 불가능한 사용자 ID로 사용합니다. **unique\_name** 클레임 또는 upn/email 클레임을 사람이 읽을 수 있는 사용자의 표시 이름으로 사용합니다. 표시 목적으로 선택적인 given\_name/family\_name 클레임을 사용할 수도 있습니다.

### 토큰 요청(OAuth2.0 코드 부여 흐름)

이제 응용 프로그램이 Azure AD에서 권한 부여 코드를 수신했으므로 Azure Resource Manager에 대한 토큰을 가져올 차례입니다. OAuth2.0 코드 부여 토큰 요청을 Azure AD 토큰 끝점에 게시:

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [권한 부여 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx) 토픽에서 설명합니다.

다음 예제에서는 암호 자격 증명을 사용하여 코드 부여 토큰에 대한 요청을 보여 줍니다.

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

인증서 자격 증명을 사용하여 작업할 때 JSON 웹 토큰(JWT)을 만들고 응용 프로그램의 인증서 자격 증명의 개인 키를 사용하여 서명합니다(RSA SHA256). 토큰에 대한 클레임 형식은 [권한 부여 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx)에 나옵니다. 참고로 클라이언트 어설션 JWT 토큰에 서명하는 방법은 [Active Directory 인증 라이브러리(.NET) 코드](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs)를 참조하세요.

클라이언트 인증에 대한 자세한 내용은 [Open ID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)을 참조하세요. 다음은 [샘플 클라이언트 어설션 JWT 토큰](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA)입니다.

다음 예제에서는 인증서 자격 증명을 사용하여 코드 부여 토큰에 대한 요청을 보여 줍니다.

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

코드 부여 토큰에 대한 응답 예제:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### 코드 부여 토큰 응답 처리

성공적인 토큰 응답은 Azure Resource Manager에 대한 (사용자 + 앱) 액세스 토큰을 포함합니다. 응용 프로그램은 이 액세스 토큰을 사용하여 사용자를 대신해 Resource Manager에 액세스합니다. Azure AD에서 발급하는 액세스 토큰의 수명은 1시간입니다. 웹 응용 프로그램이 (사용자 + 앱) 액세스 토큰을 갱신해야 할 가능성은 낮습니다. 그러나 갱신해야 한다면 응용 프로그램이 토큰 응답에서 받는 새로 고침 토큰을 사용할 수 있습니다. OAuth2.0 토큰 요청을 Azure AD 토큰 끝점에 게시:

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

새로 고침 요청에 사용할 매개 변수는 [권한 부여 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx)에서 설명합니다.

다음 예제에서는 새로 고침 토큰을 사용하는 방법을 보여 줍니다.

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

참고로 새로 고침 토큰을 사용하여 Azure Resource Manager에 대한 새 액세스 토큰을 가져올 수 있지만 이는 응용 프로그램에 의한 오프라인 액세스에 적합하지 않습니다. 새로 고침 토큰 수명은 제한되어 있고 새로 고침 토큰은 사용자에게 바인딩되기 때문입니다. 사용자가 조직을 떠나면 새로 고침 토큰을 사용하는 응용 프로그램은 액세스 권한을 잃게 됩니다. 이 방법은 Azure 리소스를 관리하는 팀이 사용하는 응용 프로그램에 적합하지 않습니다.

## 사용 가능한 구독 나열

응용 프로그램은 이제 사용자를 대신하여 Azure Resource Manager에 액세스하기 위한 토큰을 가지고 있습니다.

환경의 다음 단계는 사용자가 자신의 Azure 구독을 앱에 연결하여 사용자가 없을 때에도 앱이 해당 구독을 관리할 수 있도록 하는 것입니다(장기 오프라인 액세스). 사용자가 액세스를 관리할 수 있는 Azure 구독의 목록을 사용자에게 표시하고 사용자가 RBAC 역할을 응용 프로그램의 ID에 직접 할당할 수 있도록 합니다.

![ARM Auth - 샘플 앱 Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### 사용자가 액세스 권한을 가지고 있는 구독 나열

여기서는 먼저 [Resource Manager 목록 구독](https://msdn.microsoft.com/library/azure/dn790531.aspx) API를 호출하여 사용자가 액세스 종류를 가지고 있는 모든 구독을 나열합니다. 그런 다음 사용자가 액세스를 관리할 수 있는 구독을 식별합니다.

ASP.net MVC 샘플 앱의 [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) 메서드가 이 호출을 구현합니다.

구독 나열을 요청하는 예제:

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

구독 나열에 응답하는 예제:

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### 구독에 대한 사용자의 권한 가져오기

사용자가 액세스를 관리할 수 있는 구독에 대해서만 연결/연결 끊기 작업이 표시되어야 합니다. 각 구독에 대해 [Resource Manager 목록 권한](https://msdn.microsoft.com/library/azure/dn906889.aspx) API를 호출하여 사용자가 구독에 대한 액세스 관리 권한을 가지고 있는지 여부를 결정해야 합니다.

ASP.net MVC 샘플 앱의 [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) 메서드가 이 호출을 구현합니다.

다음 예제에서는 구독에 대 한 사용자의 권한을 요청하는 방법을 보여 줍니다. 83cfe939-2402-4581-b761-4f59b0a041e4는 구독의 ID입니다.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

구독에 대한 사용자의 권한을 가져오기 위한 응답 예제:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

사용 권한 API가 여러 사용 권한을 반환합니다. 각 사용 권한은 허용되는 작업(actions) 및 허용되지 않는 작업(notactions)으로 구성됩니다. 작업이 사용 권한의 허용되는 actions 목록에 있고 해당 권한의 notactions 목록에 없는 경우, 사용자는 해당 작업을 수행할 수 있습니다. **microsoft.authorization/roleassignments/write**는 액세스 관리 권한을 부여하는 작업입니다. 응용 프로그램은 사용 권한 결과를 구문 분석하여 각 권한의 actions 및 notactions에서 이 작업 문자열에 대한 정규식 일치(regex match)를 찾습니다.

### 필요에 따라 사용자 계정이 있는 디렉터리 나열

사용자의 계정이 여러 Azure Active 디렉터리에 있을 수 있습니다. 사용자가 초기에 올바른 디렉터리 이름을 지정하지 않은 경우, 원하는 구독이 목록에 표시되지 않을 수 있습니다.

[Resource Manager 목록 테넌트](https://msdn.microsoft.com/library/azure/dn790536.aspx) API는 사용자의 계정이 존재하는 모든 디렉터리의 ID를 나열합니다. 이 API를 호출하여 사용자의 계정이 둘 이상의 디렉터리에 있는지 여부를 결정하고 필요에 따라 사용자에게 다음과 같은 메시지를 표시할 수 있습니다. "찾고 있는 구독을 찾지 못했습니까? 사용자가 멤버인 다른 Azure Active Directory에 있을 수 있습니다. 디렉터리를 전환하려면 여기를 클릭하세요."

ASP.NET MVC 샘플 앱의 [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) 메서드가 이 호출을 구현합니다.

디렉터리를 나열하는 요청 예제:

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

디렉터리를 나열하는 응답 예제:

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## 응용 프로그램에 구독 연결

이제 사용자가 응용 프로그램에 연결할 수 있는 Azure 구독 목록을 가지고 있습니다. 다음 단계는 사용자에게 연결을 만드는 명령을 제공하는 것입니다. 사용자가 **연결**을 선택할 때 앱은 다음을 수행합니다.

1. 구독에 대한 응용 프로그램의 ID에 대해 적절한 RBAC 역할을 할당합니다.
2. 구독에 대한 응용 프로그램의 사용 권한을 쿼리하거나 응용 프로그램 전용 토큰을 사용해 Resource Manager에 액세스하여 액세스 할당의 유효성을 검사합니다.
1. 응용 프로그램의 "연결된 구독" 데이터 구조에 연결을 기록하여 구독의 ID를 유지합니다.

첫 번째 단계를 자세히 살펴보겠습니다. 응용 프로그램의 ID에 적절한 RBAC 역할을 할당하려면 다음을 결정해야 합니다.

- 사용자의 Azure Active Directory에서 응용 프로그램의 ID의 개체 ID
- 응용 프로그램이 구독에 대해 요구하는 RBAC 역할의 ID

첫 번째 부분을 자세히 살펴보겠습니다. 응용 프로그램이 Azure AD에서 사용자를 처음 인증할 때 응용 프로그램에 대한 서비스 주체 개체가 해당 Azure AD에 생성됩니다. Azure는 RBAC 역할을 서비스 주체에 할당하여 Azure 리소스에 대한 직접 액세스 권한을 해당 응용 프로그램에 부여할 수 있습니다. 이는 우리가 원하던 기능으로, 먼저 Azure AD Graph API를 쿼리하여 로그인한 사용자의 Azure AD에서 응용 프로그램의 서비스 주체의 ID를 결정합니다.

사용자는 Azure Resource Manager에 대한 액세스 토큰만 가지고 있으므로 Azure AD Graph API를 호출하려면 새 액세스 토큰이 필요합니다. Azure AD의 모든 응용 프로그램은 서비스 주체 개체를 직접 쿼리하는 권한을 가지고 있으므로 이에 대한 사용자 + 앱 액세스 토큰이 필요 없으며 응용 프로그램 전용 액세스 토큰이면 충분합니다.

<a id="app-azure-ad-graph">
### Azure AD Graph API에 대한 응용 프로그램 전용 액세스 토큰 가져오기

앱을 인증하고 Azure AD Graph API에 대한 토큰을 가져오려면 Azure AD 토큰 끝점(**https://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**)에 대한 클라이언트 자격 증명 부여 OAuth2.0 흐름 토큰 요청을 실행합니다.

ASP.net MVC 샘플 응용 프로그램의 [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) 메서드의 73-77행은 Active Directory Authentication Library for .NET를 사용하여 Graph API에 대한 응용 프로그램 전용 액세스 토큰을 가져옵니다.

클라이언트 자격 증명 부여 토큰 요청 데이터:

| 요소 | 값
|----|----
| grant\_type | **client\_credentials**
| client\_id | 응용 프로그램의 클라이언트 ID
| resource | 액세스 토큰을 요청하는 리소스의 URL 인코딩된 ID입니다. 이 경우, Azure AD Graph API의 ID: **https://graph.windows.net/**
| client\_secret 또는 client\_assertion\_type + client\_assertion | 응용 프로그램이 암호 자격 증명을 사용하는 경우, client\_secret을 사용합니다. 인증서 자격 증명을 사용하는 응용 프로그램의 경우 client\_assertion을 사용합니다.

클라이언트 자격 증명 부여 토큰에 대한 요청 예제:

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

클라이언트 자격 증명 부여 토큰에 대한 응답 예제:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### 사용자 Azure AD에서 응용 프로그램 서비스 주체의 ObjectId 가져오기

이제 응용 프로그램 전용 액세스 토큰을 사용하여 [Azure AD Graph 서비스 주체](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API를 쿼리하여 디렉터리에 있는 응용 프로그램의 서비스 주체의 개체 ID를 결정합니다.

ASP.net MVC 샘플 응용 프로그램의 [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) 메서드가 이 호출을 구현합니다.

다음 예제에서는 응용 프로그램의 서비스 주체를 요청하는 방법을 보여 줍니다. a0448380-c346-4f9f-b897-c18733de9394는 응용 프로그램의 클라이언트 ID입니다.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

다음 예제에서는 응용 프로그램의 서비스 주체 요청에 대한 응답을 보여 줍니다.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Azure RBAC 역할 ID 가져오기

선택된 구독에 대해 응용 프로그램의 서비스 주체에 적절한 RBAC 역할을 할당해야 합니다. 이를 위해 Azure RBAC 역할의 ID를 결정해야 합니다.

응용 프로그램에 대한 올바른 RBAC 역할:

- 응용 프로그램이 구독을 변경하지 않고 모니터링만 한다면 구독에 대한 독자 권한만 필요합니다. **독자** 역할을 할당합니다.
- 응용 프로그램이 Azure 구독을 관리하는 경우(엔터티 만들기/수정/삭제) 참가자 권한 중 하나가 필요합니다.
  - 특정 유형의 리소스를 관리하려면 리소스별 참가자 역할(가상 컴퓨터 참가자, 가상 네트워크 참가자, 저장소 계정 참가자 등)을 할당합니다.
  - 모든 리소스 유형을 관리하려면 **참가자** 역할을 할당합니다.

응용 프로그램에 대한 역할 할당은 사용자가 볼 수 있으므로 필요한 최소 권한을 선택합니다.

[Resource Manager 역할 정의 API](https://msdn.microsoft.com/library/azure/dn906879.aspx)를 호출하여 모든 Azure RBAC 역할을 나열하고 검색한 다음, 결과에 대해 이를 반복 실행하여 이름에 의해 원하는 역할 정의를 찾습니다.

ASP.net MVC 샘플 앱의 [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) 메서드가 이 호출을 구현합니다.

다음 요청 예제는 Azure RBAC 역할 ID를 가져오는 방법을 보여 줍니다. 09cbd307-aa71-4aca-b346-5f253e6e3ebb는 구독의 ID입니다.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

응답은 다음 형식으로 되어 있습니다.

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

참고로 이 API를 수시로 호출할 필요는 없습니다. 역할 정의의 잘 알려진 GUID를 결정한 후 다음과 같이 역할 정의 ID를 생성할 수 있습니다.

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

다음은 널리 사용되는 기본 제공 역할의 잘 알려진 GUID입니다.

| 역할 | Guid |
| ----- | ------ |
| 판독기 | acdd72a7-3385-48ef-bd42-f606fba81ae7
| 참여자 | b24988ac-6180-42a0-ab88-20f7382dd24c
| 가상 컴퓨터 참여자 | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| 가상 네트워크 참여자 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| 저장소 계정 참여자 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| 웹 사이트 참여자 | de139f84-1756-47ae-9be6-808fbbe84772
| 웹 계획 참여자 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server 참여자 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB 참여자 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### 응용 프로그램에 RBAC 역할 할당

응용 프로그램의 [Resource Manager 역할 할당 만들기](https://msdn.microsoft.com/library/azure/dn906887.aspx) API를 사용하여 선택된 구독에 대한 응용 프로그램의 서비스 주체에 적절한 RBAC 역할을 할당하기 위해 필요한 모든 것을 가졌습니다.

ASP.net MVC 샘플 앱의 [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) 메서드가 이 호출을 구현합니다.

응용 프로그램에 RBAC 역할을 할당하는 요청 예제:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

요청에 다음과 같은 값을 사용합니다.

| Guid | 설명 |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | 구독의 ID
| c3097b31-7309-4c59-b4e3-770f8406bad2 | 응용 프로그램의 서비스 주체의 개체 ID
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | 독자 RBAC 역할의 잘 알려진 GUID
| 4f87261d-2816-465d-8311-70a27558df4c | 새 역할 할당에 대해 만든 새 GUID

응답은 다음 형식으로 되어 있습니다.

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Azure Resource Manager에 대한 응용 프로그램 전용 액세스 토큰 가져오기

다음 단계는 앱이 구독에 대한 원하는 액세스 권한을 가졌는지 확인하는 것입니다. 이를 위해 Azure Resource Manager에 대한 응용 프로그램 전용 토큰을 사용하여 구독에 대한 테스트 작업을 수행해야 합니다. 오프라인 모니터링/관리를 수행하려면 테스트 작업에서 응용 프로그램이 구독에 대해 원하는 액세스 권한을 실제로 가지고 있는지 확인해야 합니다.

리소스 매개 변수에 대해 서로 다른 값을 사용하여 Azure Resource Manager에 대한 응용 프로그램 전용 액세스 토큰을 가져오려면 섹션 [Azure AD Graph API에 대한 응용 프로그램 전용 액세스 토큰 가져오기](#app-azure-ad-graph)의 지침을 따릅니다.

    https://management.core.windows.net/

ASP.net MVC 샘플 응용 프로그램의 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) 메서드의 210-214행은 Active Directory Authentication Library for .net를 사용하여 Azure Resource Manager에 대한 응용 프로그램 전용 액세스 토큰을 가져옵니다.

#### 구독에 대한 응용 프로그램의 권한 가져오기

응용 프로그램이 Azure 구독에 대해 원하는 액세스 권한을 가지고 있는지 확인하기 위해 사용자가 구독에 대해 액세스 관리 권한을 가지고 있는지 여부를 결정하는 방법과 유사한 [Resource Manager 사용 권한](https://msdn.microsoft.com/library/azure/dn906889.aspx) API를 호출할 수도 있습니다. 그러나 이번에는 이전 단계에서 수신한 응용 프로그램 전용 액세스 토큰을 사용하여 사용 권한 API를 호출합니다.

ASP.NET MVC 샘플 앱의 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) 메서드가 이 호출을 구현합니다.

## 연결된 구독 관리

구독에 대해 응용 프로그램의 서비스 주체에 적절한 RBAC 역할이 할당된 경우 응용 프로그램은 Azure Resource Manager에 대한 응용 프로그램 전용 액세스 토큰을 사용하여 해당 역할에 대한 모니터링/관리를 유지할 수 있습니다.

구독 소유자가 Azure 관리 포털 또는 명령줄 도구를 사용하여 응용 프로그램의 역할 할당을 제거하는 경우 응용 프로그램은 더 이상 해당 구독에 액세스할 수 없습니다. 이 경우 사용자에게 구독과의 연결이 응용 프로그램 외부에서 끊어졌음을 알리고 연결을 "복구"하는 옵션을 사용자에게 제공해야 합니다. 여기서 "복구"는 오프라인 상태에서 삭제된 역할 할당을 다시 만드는 것입니다.


## 구독 연결 끊기

사용자가 응용 프로그램에 대한 자신의 구독을 연결할 수 있도록 하는 것과 마찬가지로 사용자가 구독의 연결을 끊는 것도 허용해야 합니다. 액세스 관리 관점에서 연결 끊기란 응용 프로그램의 서비스 주체가 구독에 대해 가지고 있는 역할 할당을 제거하는 것을 의미합니다. 필요에 따라 구독에 대한 응용 프로그램의 상태도 제거할 수 있습니다. 구독에 대한 액세스 관리 권한을 가진 사용자만이 구독의 연결을 끊을 수 있습니다.

ASP.net MVC 샘플 앱의 [RevokeRoleFromServicePrincipalOnSubscription 메서드](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303)가 이 호출을 구현합니다.

이와 같이 사용자는 이제 응용 프로그램을 사용하여 쉽게 Azure 구독을 연결 및 관리할 수 있습니다.

<!---HONumber=AcomDC_0727_2016-->