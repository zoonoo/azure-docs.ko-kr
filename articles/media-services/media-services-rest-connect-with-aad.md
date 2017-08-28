---
title: "Azure AD 인증을 사용하여 REST로 Azure Media Services API 액세스 | Microsoft Docs"
description: "REST를 사용하여 Azure Active Directory 인증으로 Azure Media Services API를 액세스하는 방법을 알아봅니다."
services: media-services
documentationcenter: 
author: willzhan
manager: SyntaxC4
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a4531b69c44337c4863016810123f7f89bf7f98f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---

# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Azure AD 인증을 사용하여 REST로 Azure Media Services API 액세스

Azure Media Services 팀은 Azure Media Services 액세스를 위한 Azure AD(Azure Active Directory) 인증 지원을 공개했습니다. 또한 Media Services 액세스를 위한 Azure Access Control 서비스 인증을 더 이상 사용하지 않을 계획이라고 발표했습니다. 모든 Azure 구독과 Media Services 계정은 Azure AD 테넌트에 연결되며 Azure AD 인증 지원은 다양한 보안 이점을 제공합니다. 이 변경 내용과 마이그레이션에 대한 자세한 내용은 다음 블로그 게시물 및 문서를 참조하세요(앱에 Media Services .NET SDK을 사용하는 경우).

- [Azure Media Services의 Azure AD 지원 및 Access Control 인증 중단 발표](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)
- [Azure AD 인증을 사용하여 Microsoft .NET으로 Azure Media Services API 액세스](media-services-dotnet-get-started-with-aad.md)
- [Azure Portal을 사용하여 Azure AD 인증 시작](media-services-portal-get-started-with-aad.md)

일부 고객은 다음 제약 조건에 따라 해당 Media Services 솔루션을 개발해야 합니다.

*   이들은 Microsoft .NET 또는 C#이 아닌 프로그래밍 언어를 사용하거나 런타임 환경이 Windows가 아닙니다.
*   Active Directory 인증 라이브러리와 같은 Azure AD 라이브러리는 이러한 프로그래밍 언어 또는 런타임 환경에 사용할 수 없습니다.

일부 고객은 Access Control 인증 및 Azure Media Services 액세스 모두에 REST API를 사용하여 응용 프로그램을 개발했습니다. 이러한 고객에게는 Azure AD 인증 및 후속 Azure Media Services 액세스에 REST API만 사용하는 방법이 필요합니다. Azure AD 라이브러리나 Media Services .NET SDK에 의존하지 않아도 됩니다. 이 문서에서는 솔루션에 대해 설명하고 이 시나리오에 대한 심플 코드를 제공합니다. 코드는 모두 REST API 호출이고 Azure AD 또는 Azure Media Services 라이브러리에 종속성이 없으므로 코드를 다른 프로그래밍 언어로 쉽게 변환할 수 있습니다.

> [!IMPORTANT]
> 현재 Media Services는 Azure Access Control 서비스 인증 모델을 지원합니다. 하지만 Access Control 인증은 2018년 6월 1일부로 사용 중단되었습니다. 가능한 빨리 Azure AD 인증 모델로 마이그레이션하는 것이 좋습니다.


## <a name="design"></a>디자인

이 문서에서는 다음 인증 및 권한 부여 디자인을 사용합니다.

*  권한 부여 프로토콜: OAuth 2.0. OAuth 2.0은 인증 및 권한 부여 모두에 적용되는 웹 보안 표준입니다. Google, Microsoft, Facebook 및 PayPal에서 지원됩니다. 2012년 10월에 비준되었습니다. Microsoft는 OAuth 2.0 및 OpenID Connect를 지원합니다. 이러한 두 표준은 Azure Active Directory, .NET용 OWIN(Open Web Interface for .NET), Katana 및 Azure AD 라이브러리와 같은 여러 서비스 및 클라이언트 라이브러리에서 지원됩니다.
*  부여 유형: 클라이언트 자격 증명 부여 유형. 클라이언트 자격 증명은 OAuth 2.0에서 4가지 부여 유형 중 하나입니다. Azure AD Microsoft Graph API 액세스에 주로 사용됩니다.
*  인증 모드: 서비스 주체. 다른 인증 모드는 사용자 또는 대화형 인증입니다.

Media Services 사용에 대한 Azure AD 인증 및 권한 부여 흐름에는 총 4가지 응용 프로그램 또는 서비스가 포함되어 있습니다. 응용 프로그램 및 서비스, 흐름은 다음 표에 설명되어 있습니다.

|응용 프로그램 형식 |응용 프로그램 |흐름|
|---|---|---|
|클라이언트 | 고객 앱 또는 솔루션 | 이 앱(실제로는 프록시)은 Azure 구독 및 미디어 서비스 계정이 상주하는 Azure AD 테넌트에 등록됩니다. 그런 다음 등록된 앱의 서비스 주체는 미디어 서비스 계정의 액세스 제어(IAM)에서 소유자 또는 참가자 역할을 부여받습니다. 서비스 주체는 앱 클라이언트 ID 및 클라이언트 암호로 표시됩니다. |
|IDP(ID 공급자) | IDP로 Azure AD | 등록된 앱 서비스 주체(클라이언트 ID 및 클라이언트 암호)는 IDP로 Azure AD에 의해 인증됩니다. 이 인증은 내부에서 암시적으로 수행됩니다. 클라이언트 자격 증명 흐름에서와 같이 클라이언트는 사용자 대신 인증됩니다. |
|STS(보안 토큰 서비스)/OAuth 서버 | STS로 Azure AD | IDP에 의한 인증 후(또는 OAuth 2.0 조건에서 OAuth 서버), 액세스 토큰 또는 JWT(JSON Web Token)가 중간 계층 리소스(여기서는 Media Services REST API 끝점)에 액세스하기 위한 STS/OAuth 서버로서의 Azure AD에 의해 발급됩니다. |
|리소스 | Media Services REST API | 모든 Media Services REST API 호출은 STS 또는 OAuth 서버로서 Azure AD에서 발급한 액세스 토큰으로 권한 부여됩니다. |

샘플 코드를 실행하고 JWT 또는 액세스 토큰을 캡처하면 JWT에 다음 특성이 있습니다.

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

다음은 JWT에 있는 특성과 이전 표에서 4가지 응용 프로그램 또는 서비스 간의 매핑입니다.

|응용 프로그램 형식 |응용 프로그램 |JWT 특성 |
|---|---|---|
|클라이언트 |고객 앱 또는 솔루션 |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". 다음 섹션에서 Azure AD에 등록할 응용 프로그램의 클라이언트 ID입니다. |
|IDP(ID 공급자) | IDP로 Azure AD |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  GUID는 Microsoft 테넌트의 ID입니다(microsoft.onmicrosoft.com). 각 테넌트가 자기만의 ID를 가지고 있습니다. |
|STS(보안 토큰 서비스)/OAuth 서버 |STS로 Azure AD | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". GUID는 Microsoft 테넌트의 ID입니다(microsoft.onmicrosoft.com). |
|리소스 | Media Services REST API |aud: "https://rest.media.azure.net". 액세스 토큰의 받는 사람 또는 대상입니다. |

## <a name="steps-for-setup"></a>설치 단계

Azure AD 인증을 위해 Azure AD 응용 프로그램을 등록 및 설정하고 Azure Media Services REST API 끝점 호출을 위해 액세스 토큰을 가져오려면 다음 단계를 완료합니다.

1.  [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에서 Azure AD 응용 프로그램(예를 들어, wzmediaservice)을 Azure AD 테넌트(예를 들어, microsoft.onmicrosoft.com)에 등록합니다. 웹앱 또는 원시 앱으로 등록했는지 여부는 중요하지 않습니다. 또한 로그온 URL 및 회신 URL(예를 들어, 둘 다로 http://wzmediaservice.com )을 선택할 수 있습니다.
2. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에서 응용 프로그램의 **구성** 탭으로 이동합니다. **클라이언트 ID**를 적어둡니다. 그런 다음 **키** 아래에서 **클라이언트 키**(클라이언트 암호)를 생성합니다. 

    > [!NOTE] 
    > 클라이언트 암호를 기록해 둡니다. 다시 표시되지 않습니다.
    
3.  [Azure Portal](http://ms.portal.azure.com)에서 Media Services 계정으로 이동합니다. **액세스 제어(IAM)** 창을 선택합니다. 소유자 또는 참가자 역할의 새 멤버를 추가합니다. 보안 주체의 경우, 1단계에서 등록한 응용 프로그램 이름을 검색합니다(이 예에서 wzmediaservice).

## <a name="info-to-collect"></a>수집할 정보

REST 코딩을 준비하려면 코드에 포함할 다음 데이터 요소를 수집합니다.

*   STS 끝점으로 Azure AD: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. 이 끝점에서 JWT 액세스 토큰이 요청됩니다. Azure AD는 IDP 외에 STS로도 사용됩니다. Azure AD는 리소스 액세스를 위한 JWT(액세스 토큰)를 발급합니다. JWT 토큰은 다양한 클레임을 포함합니다.
*   리소스 또는 대상으로 Azure Media Services REST API: https://rest.media.azure.net.
*   클라이언트 ID: [설치 단계](#steps-for-setup)의 2단계를 참조하세요.
*   클라이언트 암호: [설치 단계](#steps-for-setup)의 2단계를 참조하세요.
*   다음과 같은 형식의 Media Services 계정 REST API 끝점:

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    응용 프로그램에서 모든 Media Services REST API 호출이 수행되는 끝점입니다. 예를 들어, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

그런 다음 코드에 사용하기 위해 이러한 5개의 매개 변수를 web.config 또는 app.config 파일에 넣을 수 있습니다.

## <a name="sample-code"></a>샘플 코드

[Azure Media Services 액세스를 위한 Azure AD 인증: 둘 다 REST API를 통해](https://github.com/willzhan/WAMSRESTSoln)에서 샘플 코드를 확인할 수 있습니다.

샘플 코드는 두 부분으로 구성되어 있습니다.

*   Azure AD 인증 및 권한 부여를 위한 모든 REST API 코드를 포함하는 DLL 라이브러리 프로젝트. 또한 액세스 토큰을 이용해 Media Services REST API 끝점에 대해 REST API 호출을 수행하는 메서드도 포함합니다.
*   Azure AD 인증을 시작하고 다양한 Media Services REST API를 호출하는 콘솔 테스트 클라이언트.

샘플 프로젝트는 3가지 기능을 포함합니다.

*   REST API만 사용하여 클라이언트 자격 증명 부여를 통한 Azure AD 인증.
*   REST API만 사용하여 Azure Media Services 액세스.
*   REST API만 사용하여 Azure Storage 액세스(REST API를 사용하여 Media Services 계정을 만드는 데 사용됨).


## <a name="where-is-the-refresh-token"></a>새로 고침 토큰 위치

일부 독자는 새로 고침 토큰이 있는 위치를 궁금해할 수 있습니다. 여기서 새로 고침을 사용하지 않는 이유

새로 고침 토큰의 용도는 액세스 토큰을 새로 고치는 것이 아닙니다. 대신 최종 사용자 인증이나 사용자 개입을 바이패스하고 이전 토큰이 만료될 때 유효한 액세스 토큰을 계속 가져오기 위해 설계되었습니다. 새로 고침 토큰에는 “사용자 다시 로그인 바이패스 토큰”과 같은 표현이 더 적절한 이름일 수 있습니다.

OAuth 2.0 권한 부여 흐름(사용자 이름 및 암호, 사용자를 대신하여 작동)을 사용하는 경우 새로 고침 토큰을 사용하여 사용자 개입을 요청하지 않고도 갱신된 액세스 토큰을 가져올 수 있습니다. 하지만 이 문서에서 설명한 OAuth 2.0 클라이언트 자격 증명 부여 흐름의 경우 클라이언트는 자체적으로 작동합니다. 사용자 개입이 전혀 필요하지 않으며 권한 부여 서버에서 사용자에게 새로 고침 토큰을 제공하지 않아도 되며 제공하지 않습니다. **GetUrlEncodedJWT** 메서드를 디버깅하는 경우 토큰 끝점에서의 응답에는 액세스 토큰이 있지만 새로 고침 토큰은 없습니다.

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-dotnet-upload-files.md) 시작

