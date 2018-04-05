---
title: Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API 보호 | Microsoft Docs
description: Azure Active Directory 및 API Management로 Web API 백 엔드를 보호하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API를 보호하는 방법

이 가이드에서는 AAD(Azure Active Directory)에서 OAuth 2.0 프로토콜을 사용하여 API를 보호하도록 APIM(API Management) 인스턴스를 구성하는 방법을 보여 줍니다. 

## <a name="prerequisite"></a>필수 요소
이 문서의 단계를 따르려면 다음이 있어야 합니다.
* APIM 인스턴스.
* API APIM 인스턴스를 사용하여 게시할 API
* Azure AD 테넌트

## <a name="overview"></a>개요

이 가이드에서는 APIM에서 OAuth 2.0으로 API를 보호하는 방법을 보여 줍니다. 이 문서에서는 Azure AD가 권한 부여 서버(OAuth 서버)로 사용됩니다. 다음은 간단한 단계 개요입니다.

1. API를 나타내기 위해 Azure AD에 응용 프로그램(backend-app) 등록
2. API 호출에 필요한 클라이언트 응용 프로그램을 나타내기 위해 Azure AD에 다른 응용 프로그램(client-app) 등록
3. Azure AD에서 client-app에 backend-app을 호출하기 위한 권한 부여
4. OAuth 2.0 사용자 권한 부여를 사용하도록 개발자 콘솔 구성
5. validate-jwt 정책을 추가하여 들어오는 모든 요청에 대해 OAuth 토큰 유효성 검사

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API를 나타내기 위해 Azure AD에 응용 프로그램 등록

Azure AD에서 API를 보호하려는 경우 첫 번째 단계는 API를 나타내는 응용 프로그램을 Azure AD에서 등록하는 것입니다. 

Azure AD 테넌트로 이동한 다음, **앱 등록**으로 이동합니다.

**새 응용 프로그램 등록**을 선택합니다. 

응용 프로그램 이름을 지정합니다. 이 예제에서는 `backend-app`이 사용됩니다.  

**응용 프로그램 유형**으로 **웹앱/API**를 선택합니다. 

**로그온 URL**에 대해 `https://localhost`를 자리 표시자로 사용할 수 있습니다.

**만들기**를 클릭합니다.

응용 프로그램이 만들어지면 이후 단계에서 사용할 수 있게 **응용 프로그램 ID**를 적어둡니다. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>클라이언트 응용 프로그램을 나타내기 위해 Azure AD에 다른 응용 프로그램 등록

API를 호출해야 하는 모든 클라이언트 응용 프로그램을 Azure AD에서도 응용 프로그램으로 등록해야 합니다. 이 가이드에서는 APIM 개발자 포털의 개발자 콘솔을 샘플 클라이언트 응용 프로그램으로 사용합니다. 

개발자 콘솔을 나타내기 위해 Azure AD에 다른 응용 프로그램을 등록해야 합니다.

**새 응용 프로그램 등록**을 다시 클릭합니다. 

응용 프로그램의 이름을 지정하고 **응용 프로그램 유형**으로 **웹앱/API**를 선택합니다. 이 예제에서는 `client-app`이 사용됩니다.  

**로그온 URL**에 대해 `https://localhost`를 자리 표시자로 사용하거나 APIM 인스턴스의 로그인 URL을 사용할 수 있습니다. 이 예제에서는 `https://contoso5.portal.azure-api.net/signin`이 사용됩니다.

**만들기**를 클릭합니다.

응용 프로그램이 만들어지면 이후 단계에서 사용할 수 있게 **응용 프로그램 ID**를 적어둡니다. 

이제 후속 단계에서 사용하기 위해 이 응용 프로그램에 대한 클라이언트 암호를 만들어야 합니다.

**설정**을 다시 클릭하고 **키**로 이동합니다.

**암호**에서 **키 설명**을 제공하고 키 만료 시기를 선택한 후 **저장**을 클릭합니다.

키 값을 적어둡니다. 

## <a name="grant-permissions-in-aad"></a>AAD에서 권한 부여

이제 API(즉, backend-app) 및 개발자 콘솔(즉, client-app)을 나타내기 위해 2개의 응용 프로그램을 등록했으므로, client-app에 backend-app을 호출하기 위한 권한을 부여해야 합니다.  

**응용 프로그램 등록**으로 다시 이동합니다. 

`client-app`을 클릭하고 **설정**으로 이동합니다.

**필요한 권한**을 클릭하고 **추가**를 클릭합니다.

**API 선택**을 클릭하고 `backend-app`을 검색합니다.

**위임된 권한**에서 `Access backend-app`을 선택합니다. 

**선택**을 클릭하고 **완료**를 클릭합니다. 

> [!NOTE]
> **Windows** **Azure Active Directory**가 다른 응용 프로그램에 대한 사용 권한에 나열되지 않으면 **추가**를 클릭하고 목록에서 추가합니다.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>개발자 포털에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정

이제 Azure AD에서 응용 프로그램을 만들었으며 client-app에 backend-app을 호출하기 위한 적절한 권한을 부여했습니다. 

이 가이드에서는 개발자 콘솔을 client-app으로 사용합니다. 아래 단계에서는 개발자 포털에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정하는 방법을 설명합니다. 

APIM 인스턴스로 이동합니다.

**OAuth 2.0**을 클릭하고 **추가**를 클릭합니다.

**표시 이름** 및 **설명**을 제공합니다.

클라이언트 등록 페이지 URL에 대해 **`http://localhost`와 같은 자리 표시자 값을 입력합니다.  **클라이언트 등록 페이지 URL** 은 사용자가 계정의 사용자 관리를 지원하는 OAuth 2.0 공급자에 대한 계정을 만들고 구성하는 데 사용할 수 있는 페이지를 가리킵니다. 이 예에서 자리 표시자를 사용하도록 사용자는 자신의 계정을 만들거나 구성하지 않습니다.

**권한 부여 코드**를 **권한 부여 유형**으로 선택합니다.

그런 다음 **권한 부여 끝점 URL** 및**토큰 끝점 URL**을 지정합니다.

이러한 값은 Azure AD 테넌트의 **끝점** 페이지에서 검색할 수 있습니다. 끝점에 액세스하려면 **앱 등록** 페이지로 다시 이동한 후 **끝점**을 클릭합니다.

**OAuth 2.0 권한 부여 끝점**을 복사하여 **권한 부여 끝점 URL** 텍스트 상자에 붙여 넣습니다.

**OAuth 2.0 토큰 끝점**을 복사하여 **토큰 끝점 URL** 텍스트 상자에 붙여 넣습니다.

토큰 끝점 외에도, **resource**라는 추가 본문 매개 변수를 추가하고, 해당 값으로 backend-app에 대한 **응용 프로그램 ID**를 사용합니다.

다음으로 클라이언트 자격 증명을 지정합니다. 이러한 자격 증명은 client-app에 대한 자격 증명입니다.

**클라이언트 ID**로 client-app에 대한 **응용 프로그램 ID**를 사용합니다.

**클라이언트 암호**로 이전에 client-app에 대해 만든 키를 사용합니다. 

클라이언트 암호 바로 뒤에는 권한 부여 코드 권한 유형에 대한 **redirect_url**이 나옵니다.

이 URL을 적어둡니다.

**만들기**를 클릭합니다.

client-app의 **설정** 페이지로 다시 이동합니다.

**회신 URL**을 클릭하고 **redirect_url**을 첫 번째 행에 붙여 넣습니다. 이 예제에서는 `https://localhost`를 첫 번째 행의 URL로 바꾸었습니다.  

이제 OAuth 2.0 권한 부여 서버를 구성했으므로 개발자 콘솔에서 Azure AD의 액세스 토큰을 가져올 수 있습니다. 

다음 단계는 API에 대해 OAuth 2.0 사용자 권한 부여를 사용하도록 설정하여 개발자 콘솔에서 API를 호출하기 전에, 사용자 대신 액세스 토큰을 가져와야 한다는 사실을 알 수 있도록 하는 것입니다.

APIM 인스턴스로 가서 **API**로 이동합니다.

보호하려는 API를 클릭합니다. 이 예제에서는 `Echo API`를 사용합니다.

**설정**으로 이동합니다.

**보안** 아래에서 **OAuth 2.0**을 선택하고 이전에 구성한 OAuth 2.0 서버를 선택합니다. 

**Save**를 클릭합니다.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>개발자 포털에서 API 호출 성공

`Echo API`에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정했으므로 개발자 콘솔은 API를 호출하기 전에, 사용자 대신 액세스 토큰을 획득합니다.

개발자 포털에서 `Echo API` 아래의 임의 작업으로 이동한 후 **사용해 보기**를 클릭하여 개발자 콘솔로 이동합니다.

방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

권한 부여 드롭다운 목록에서 **권한 부여 코드**를 선택하면 Azure AD 테넌트에 로그인하라는 메시지가 표시됩니다. 이미 이 계정으로 로그인한 경우 메시지가 나타나지 않을 수 있습니다.

성공적으로 로그인한 후 `Authorization` 헤더가 Azure AD의 액세스 토큰과 함께 요청에 추가됩니다. 

샘플 토큰은 아래와 같으며 Base64로 인코딩됩니다.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

**보내기**를 클릭하면 API를 성공적으로 호출할 수 있습니다.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>미리 요청 권한을 부여하도록 JWT 유효성 검사 정책 구성

이때 사용자가 개발자 콘솔에서 호출을 수행하려고 하면 로그인하라는 메시지가 표시되며, 개발자 콘솔은 사용자 대신, 액세스 토큰을 획득합니다. 모든 것이 예상대로 작동됩니다. 그러나 토큰이 없거나 잘못된 토큰이 있는 상태에서 API를 호출하면 어떻게 될까요? 예를 들어, `Authorization` 헤더를 삭제한 상태에서도 API를 여전히 호출할 수 있습니다. 그 이유는 APIM인 지금은 액세스 토큰이 유효한지 검사하지 않기 때문입니다. APIM은 백 엔드 API에 `Auhtorization` 헤더를 전달합니다.

APIM에서 [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 들어오는 각 요청의 액세스 토큰에 대한 유효성을 검사함으로써 해당 요청을 미리 인증할 수 있습니다. 요청에 유효한 토큰이 없으면 API Management에 의해 차단되며 요청이 백 엔드로 전달되지 않습니다. 아래 정책을 `Echo API`에 추가할 수 있습니다. 

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="next-steps"></a>다음 단계
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
