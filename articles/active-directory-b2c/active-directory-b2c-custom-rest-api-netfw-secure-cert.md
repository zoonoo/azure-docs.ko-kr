---
title: "Azure Active Directory B2C: 클라이언트 인증서를 사용하여 RESTful 서비스 보호"
description: "클라이언트 인증서를 사용하여 Azure AD B2C에서 사용자 지정 REST API 클레임 교환을 보호하는 방법 샘플"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C: 클라이언트 인증서를 사용하여 RESTful 서비스 보호
[RESTful 서비스를 만들어](active-directory-b2c-custom-rest-api-netfw.md) Azure AD B2C와 상호 작용한 후에 클라이언트 인증서를 사용하여 Azure 웹앱(RESTful API)에 대한 액세스를 제한하는 방법을 제공합니다. 이 메커니즘은 TLS 상호 인증 또는 **클라이언트 인증서 인증**이라고 합니다.  적절한 인증서를 포함한 Azure AD B2C와 같은 서비스만 서비스에 액세스할 수 있습니다.

> [!NOTE]
>
>또한 [HTTP 기본 인증을 사용하여](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) RESTful 서비스를 보호할 수 있습니다. 그러나 클라이언트 인증서를 통한 HTTP 기본 인증의 보안 수준은 낮은 것으로 간주된다는 점을 기억하세요. 이 문서에 설명된 대로 클라이언트 인증서 인증을 사용하여 RESTful 서비스를 보호하는 것이 좋습니다.

이 문서에서는 다음과 같은 방법을 자세히 설명합니다.
1. 클라이언트 인증서 인증을 사용하도록 웹앱 설정
1. Azure AD B2C 정책 키에 인증서 업로드
1. 클라이언트 인증서를 사용하도록 사용자 지정 정책 구성

## <a name="prerequisites"></a>필수 조건
* [REST API 클레임 교환 통합](active-directory-b2c-custom-rest-api-netfw.md) 문서에서 이전 단계 완료
* 유효한 인증서(개인 키를 포함한 .pfx 파일)

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>1단계: 클라이언트 인증서 인증에 대한 Web App 구성
**Azure App Service**가 클라이언트 인증서를 요구하도록 설정하려면 웹앱 `clientCertEnabled` 사이트 설정이 true여야 합니다. 이 설정은 현재 Azure Portal의 관리 환경을 통해 사용할 수 없으며, REST API를 사용하여 이를 변경해야 합니다.

> [!NOTE]
>
>Azure App Service 계획이 표준 이상인지 확인합니다. 자세한 내용은 [Azure App Service 계획 심층 개요](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)를 참조하세요.


[Azure Resource Explorer(미리 보기)](https://resources.azure.com)를 사용하여 clientCertEnabled 속성을 true로 대칭 이동합니다. 다음 스크린샷에서는 Azure Resource Explorer를 통해 clientCertEnabled 값을 설정하는 방법을 보여줍니다. ![Azure Resource Explorer를 통해 clientCertEnabled 설정](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>clientCertEnabled 특성을 설정하는 방법에 대한 자세한 내용은 [Web App에 대한 TLS 상호 인증을 구성하는 방법](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)을 참조하세요.


> [!TIP]
>
>또는 [ARMClient](https://github.com/projectkudu/ARMClient) 도구를 사용하여 REST API 호출을 쉽게 만들 수 있습니다.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2단계: Azure AD B2C 정책 키에 인증서 업로드
`clientCertEnabled`를 `true`로 설정한 후에는 RESTful API와 통신하기 위해 클라이언트 인증서가 필요합니다. 그렇게 하기 위해 Azure AD B2C 테넌트에서 클라이언트 인증서를 업로드하고 저장해야 합니다.   
1.  Azure AD B2C 테넌트로 이동하고 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.
2.  **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.
3.  **+추가**를 클릭합니다.
4.  **옵션**에는 **업로드**를 사용합니다.
5.  **이름**에는 `B2cRestClientCertificate`를 사용합니다.  
    `B2C_1A_` 접두사가 자동으로 추가됩니다.
6.  **파일 업로드**에서 개인 키가 있는 인증서 .pfx 파일을 선택합니다. 인증서의 **암호**를 입력합니다.

    ![정책 키 업로드](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  **만들기**
8.  만든 키 `B2C_1A_B2cRestClientCertificate`를 확인하려면 **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>3단계: 확장 정책에서 클라이언트 인증서 인증을 지원하도록 `TechnicalProfile` 변경
1.  작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
2.  `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 찾습니다.
3.  `<Metadata>` 요소를 찾습니다.
4.  `AuthenticationType`를 `ClientCertificate`로 변경합니다.

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  `<Metadata>` 요소를 닫은 후에 바로 다음 XML 코드 조각을 추가합니다.  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

XML 코드 조각을 추가한 후에 `TechnicalProfile`은 다음과 같아야 합니다.

![ClientCertificate 인증 XML 요소를 설정합니다.](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4단계: 테넌트에 정책 업로드

1.  [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C**를 클릭합니다.
2.  **ID 경험 프레임워크**를 선택합니다.
3.  **모든 정책**을 클릭합니다.
4.  **정책 업로드**를 선택합니다.
5.  **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
6.  TrustFrameworkExtensions.xml을 **업로드**하고 유효성 검사가 실패하지 않았는지 확인합니다.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5단계: 지금 실행을 사용하여 사용자 지정 정책 테스트
1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.

    >[!NOTE]
    >
    >    **지금 실행**을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 
    >    응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.

2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 엽니다. **지금 실행**을 선택합니다.

3.  **지정된 이름** 필드에서 "Test"를 입력해 보세요. B2C에서 페이지의 위쪽에 오류 메시지를 표시합니다.

    ![ID API 테스트](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  **지정된 이름** 필드에서 ("Test" 이외의) 이름을 입력해 보세요. B2C는 사용자를 등록한 후 loyaltyNumber를 응용 프로그램에 보냅니다. 이 예제에서 이 JWT의 번호를 적어둡니다.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

   > [!NOTE]
   >위의 오류 메시지가 발생하는 경우 클라이언트 인증서가 표시되는 동안 Azure AD B2C에서 RESTful 서비스를 호출하는 데 성공했다는 의미입니다. 다음 단계에서는 인증서의 유효성을 검사합니다.

## <a name="step-6-adding-certificate-validation"></a>6단계: 인증서 유효성 검사 추가
Azure AD B2C에서 RESTful 서비스에 전송한 클라이언트 인증서는 Azure Web Apps 플랫폼에 의한 유효성 검사를 거치지 않습니다(인증서가 있는 검사 제외). 인증서의 유효성을 검사하는 작업은 웹앱에서 담당합니다. 인증을 위해 인증서 속성의 유효성을 검사하는 샘플 ASP.NET 코드는 다음과 같습니다.

> [!NOTE]
>클라이언트 인증서 인증을 위해 Azure Service App을 구성하는 방법에 대한 자세한 내용은 [웹앱에 대한 TLS 상호 인증을 구성하는 방법](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)을 참조하세요.

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 프로젝트의 web.config 파일에 응용 프로그램 설정 추가
앞에서 만든 Visual Studio 프로젝트를 열고 `appSettings` 요소 아래의 web.config 파일에 다음 응용 프로그램 설정을 추가합니다.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

다음과 같은 인증서의 값을 바꿉니다.
* 주체 이름
* 발급자 이름
* 인증서 지문

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 IsValidClientCertificate 함수 추가
Controllers\IdentityController.cs를 열고 다음 함수를 `Identity` 컨트롤러 클래스에 추가합니다. 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

앞의 **예제**에서 모든 조건이 충족될 경우에만 인증서를 유효한 것으로 수용합니다.
1. 인증서가 만료되지 않고 현재 서버에서 활성화되어 있습니다.
2. 인증서의 `Subject` 이름에는 `ClientCertificate:Subject` 응용 프로그램 설정 값과 같은 공통 이름이 있습니다.
3. 인증서의 `Issuer` 이름에는 `ClientCertificate:Issuer` 응용 프로그램 설정 값과 같은 공통 이름이 있습니다.
4. 인증서의 `thumbprint`은 `ClientCertificate:Thumbprint` 응용 프로그램 설정 값과 같습니다.

> [!IMPORTANT]
>
>서비스의 중요도에 따라 더 많은 유효성 검사를 추가해야 합니다. 예를 들어 인증서를 신뢰할 수 있는 루트 인증 기관에 연결했는지 테스트, 발급자 조직 이름 유효성 검사 등입니다.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 IsValidClientCertificate 함수 추가
Controllers\IdentityController.cs를 열고 `SignUp()` 함수의 맨 앞에 다음 코드 줄을 추가합니다. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

코드 조각을 추가한 후에 `Identity` 컨트롤러는 다음과 같아야 합니다.

![인증서 유효성 검사 코드 추가](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>7단계: Azure에 게시 및 테스트
1. **솔루션 탐색기**에서 **Contoso.AADB2C.API** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
2. 6단계를 반복하고 인증서 유효성 검사를 사용하여 사용자 지정 정책을 (다시) 테스트합니다. 정책을 실행하고 유효성 검사를 추가한 후에 제대로 작동하는지 확인합니다.
3. 이제 web.config 파일에서 `ClientCertificate:Subject`의 값을 **잘못된** 값으로 변경합니다. 정책을 다시 실행하면 오류 메시지가 표시됩니다.
4. 다시 값을 **유효한** 값으로 변경하고 정책이 REST API를 호출할 수 있는지 확인합니다.
5. [Application Insights를 사용하여 로그를 수집](active-directory-b2c-troubleshoot-custom.md)하여 문제 해결.

## <a name="optional-download-the-complete-policy-files-and-code"></a>[선택 사항]완성 정책 파일 및 코드 다운로드
* 이러한 샘플 파일을 사용하는 대신 사용자 지정 정책을 사용하여 시작을 완료한 후에 고유한 사용자 지정 정책 사용하여 시나리오를 빌드하는 것이 좋습니다.  [참조할 샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* 여기에서 전체 코드를 다운로드할 수 있습니다. [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)