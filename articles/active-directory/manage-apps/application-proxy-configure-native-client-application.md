---
title: 네이티브 클라이언트 앱 게시 - Azure AD | Microsoft Docs
description: Azure AD 애플리케이션 프록시 커넥터와 통신하는 네이티브 클라이언트 앱을 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825496"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>네이티브 클라이언트 응용 프로그램 프록시 응용 프로그램과 상호 작용을 사용 하는 방법

Azure Active Directory (Azure AD) 응용 프로그램 프록시를 사용 하 여 웹 앱을 게시 하지만 사용할 수 있습니다 사용 하 여 Azure AD 인증 라이브러리 (ADAL)를 구성 된 네이티브 클라이언트 응용 프로그램을 게시 합니다. 네이티브 클라이언트 애플리케이션은 디바이스에 설치되는 반면 웹앱은 브라우저를 통해 액세스되므로 웹앱과 다릅니다.

네이티브 클라이언트 응용 프로그램을 지원 하려면 응용 프로그램 프록시는 헤더에 전송 되는 Azure AD에서 발급 한 토큰을 허용 합니다. 응용 프로그램 프록시 서비스는 사용자에 대 한 인증을 수행합니다. 이 솔루션 인증에 대 한 응용 프로그램 토큰을 사용 하지 않습니다.

![최종 사용자, Azure Active Directory 및 게시된 애플리케이션 간의 관계](./media/application-proxy-configure-native-client-application/richclientflow.png)

네이티브 응용 프로그램을 게시 하려면 인증을 처리 하 고 다양 한 클라이언트 환경을 지 원하는 Azure AD 인증 라이브러리를 사용 합니다. 애플리케이션 프록시는 [Web API 시나리오에 대한 네이티브 애플리케이션](../develop/native-app.md)에 맞습니다.

이 문서에서는 애플리케이션 프록시 및 Azure AD 인증 라이브러리를 사용하여 네이티브 애플리케이션을 게시하는 네 가지 단계를 안내합니다.

## <a name="step-1-publish-your-proxy-application"></a>1단계: 프록시 응용 프로그램 게시

다른 애플리케이션과 마찬가지로 프록시 애플리케이션을 게시하고 애플리케이션에 액세스하도록 사용자를 할당합니다. 자세한 내용은 [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요.

## <a name="step-2-register-your-native-application"></a>2단계: 네이티브 응용 프로그램 등록

이제 다음과 같이 Azure AD에서 응용 프로그램을 등록 해야 합니다.

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/)합니다. 합니다 **대시보드** 에 대 한 합니다 **Azure Active Directory 관리 센터** 나타납니다.
2. 선택 보충에서 **Azure Active Directory**합니다. 합니다 **Azure Active Directory** 개요 페이지가 나타납니다.
3. Azure AD 개요 사이드바에서 선택 **앱 등록**합니다. 모든 앱 등록의 목록이 표시 됩니다.
4. **새 등록**을 선택합니다. 합니다 **응용 프로그램을 등록** 페이지가 나타납니다.

   ![새 앱 등록 만들기](./media/application-proxy-configure-native-client-application/create.png)
5. 에 **이름을** 응용 프로그램에 대 한 사용자 측 표시 이름을 지정 제목입니다.
6. 아래는 **지원 되는 계정 유형** 다음이 지침에 따라 액세스 수준을 선택한 제목:
   - 내부 조직에 있는 계정만 대상으로 선택 **만이 조직 디렉터리의 계정**합니다.
   - 비즈니스 또는 교육용 고객을 대상으로 선택 **모든 조직 디렉터리의 계정**합니다.
   - 광범위 한 Microsoft id 집합을 대상으로 선택 **계정이 모든 조직 디렉터리에 개인 Microsoft 계정**합니다.
7. 에 **리디렉션 URI** 을 선택 **공용 클라이언트 (모바일 및 데스크톱)** , 응용 프로그램의 리디렉션 URI를 입력 합니다.
8. 선택 하 여 읽어 합니다 **Microsoft 플랫폼 정책을**를 선택한 후 **등록**합니다. 새 응용 프로그램 등록에 대 한 개요 페이지 생성 되어 표시 됩니다.

새 응용 프로그램 등록을 만드는 방법은 자세한 [Azure Active Directory와 응용 프로그램 통합](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)합니다.

## <a name="step-3-grant-access-to-your-proxy-application"></a>3단계: 프록시 응용 프로그램에 액세스 권한 부여

네이티브 응용 프로그램을 등록 했으므로 지정할 수 있습니다 액세스 다른 응용 프로그램에 디렉터리에이 경우 프록시 응용 프로그램에 액세스 합니다. 네이티브 응용 프로그램 프록시 응용 프로그램에 노출 될 수 있도록 합니다.

1. 새 응용 프로그램 등록 페이지의 세로 막대를 선택 **API 사용 권한**합니다. 합니다 **API 사용 권한** 페이지에 새 응용 프로그램 등록이 나타납니다.
2. **사용 권한 추가**를 선택합니다. 합니다 **API 요청 권한을** 페이지가 나타납니다.
3. 아래는 **API 선택** 설정 선택 **내 조직에서 사용 하는 Api**합니다. Api를 노출 하는 디렉터리에 응용 프로그램이 포함 된 목록이 나타납니다.
4. 프록시 응용 프로그램에서 게시를 찾으려면 검색 상자에 스크롤 입력 [1 단계: 프록시 응용 프로그램을 게시](#step-1-publish-your-proxy-application), 한 다음 프록시 응용 프로그램을 선택 합니다.
5. 에 **사용 권한 유형을 응용 프로그램에 필요한?** 사용 권한 유형을 선택 제목입니다. 네이티브 응용 프로그램에 로그인 한 사용자로 응용 프로그램 프록시 API에 액세스 해야 하는 경우 선택할 **위임 된 권한**합니다. 백그라운드 서비스 또는 디먼에서 로그인 한 사용자 없이 다른 여러 가지 기본 응용 프로그램을 실행 하는 경우 선택할 **응용 프로그램 사용 권한**합니다.
6. 에 **권한을 선택** 머리글을 원하는 권한을 선택 및 선택 **권한을 추가**합니다. **API 사용 권한** 페이지에 이제 네이티브 응용 프로그램 프록시 추가한 응용 프로그램 및 사용 권한 API를 표시 합니다.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4단계: Active Directory 인증 라이브러리 편집

Active Directory 인증 라이브러리(ADAL)의 인증 컨텍스트에서 네이티브 애플리케이션 코드를 편집하여 다음 텍스트를 포함합니다.

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

샘플 코드에서 필요한 정보는 다음과 같이 Azure AD 포털에서 찾을 수 있습니다.

| 정보 필요 | Azure AD 포털의 검색 방법 |
| --- | --- |
| \<테 넌 트 ID > | **Azure Active Directory** > **속성** > **디렉터리 ID** |
| \<프록시 앱의 외부 Url > | **엔터프라이즈 응용 프로그램** > *프록시 응용 프로그램* > **응용 프로그램 프록시** > **외부 Url** |
| \<네이티브 앱의 앱 ID > | **엔터프라이즈 응용 프로그램** > *네이티브 응용 프로그램* > **속성** > **응용 프로그램 ID** |
| \<네이티브 앱의 리디렉션 URI > | **Azure Active Directory** > **앱 등록** > *네이티브 응용 프로그램이* > **리디렉션 Uri** |
| \<프록시 앱 API Url > | **Azure Active Directory** > **앱 등록** > *네이티브 응용 프로그램이* > **API 사용 권한**  >  **API / 사용 권한 이름** |

이러한 매개 변수를 사용 하 여 ADAL을 편집한 후 사용자가 회사 네트워크 외부에 있을 경우에 네이티브 클라이언트 응용 프로그램에 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

네이티브 응용 프로그램 흐름에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory에서 네이티브 앱](../develop/native-app.md)합니다.

설정에 대해 알아보기 [Azure Active Directory에서 응용 프로그램에서 Single sign-on](what-is-single-sign-on.md#choosing-a-single-sign-on-method)합니다.
