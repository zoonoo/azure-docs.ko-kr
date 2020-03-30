---
title: Azure AD 응용 프로그램 프록시를 사용 하 고 온-프레미스 API에 액세스
description: Azure Active Directory의 응용 프로그램 프록시를 사용하면 네이티브 앱이 온-프레미스 또는 클라우드 VM에서 호스팅하는 API 및 비즈니스 논리에 안전하게 액세스할 수 있습니다.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166002"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 API에 대한 보안 액세스

온-프레미스에서 실행되는 비즈니스 논리 API가 있거나 클라우드의 가상 컴퓨터에서 호스팅될 수 있습니다. 네이티브 Android, iOS, Mac 또는 Windows 앱은 데이터를 사용하거나 사용자 상호 작용을 제공하기 위해 API 끝점과 상호 작용해야 합니다. Azure AD 응용 프로그램 프록시 및 [Azure Active Directory 인증 라이브러리(ADAL)를](/azure/active-directory/develop/active-directory-authentication-libraries) 통해 네이티브 앱이 온-프레미스 API에 안전하게 액세스할 수 있습니다. Azure Active Directory 응용 프로그램 프록시는 방화벽 포트를 열고 앱 계층에서 인증 및 권한 부여를 제어하는 것보다 더 빠르고 안전한 솔루션입니다. 

이 문서에서는 네이티브 앱이 액세스할 수 있는 웹 API 서비스를 호스팅하기 위한 Azure AD 응용 프로그램 프록시 솔루션을 설정하는 방법을 안내합니다. 

## <a name="overview"></a>개요

다음 다이어그램에서는 온-프레미스 API를 게시하는 기존 방법을 보여 주었습니다. 이 방법을 사용하려면 들어오는 포트 80 및 443을 열어야 합니다.

![기존 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

다음 다이어그램에서는 들어오는 포트를 열지 않고 Azure AD 응용 프로그램 프록시를 사용하여 API를 안전하게 게시하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여

![Azure AD 응용 프로그램 프록시 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 응용 프로그램 프록시는 API 액세스를 위한 공용 끝점으로 작업하고 인증 및 권한 부여를 제공하면서 솔루션의 중추를 형성합니다. [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) 라이브러리를 사용하여 광범위한 플랫폼에서 API에 액세스할 수 있습니다. 

Azure AD 응용 프로그램 프록시 인증 및 권한 부여는 Azure AD 위에 빌드되므로 Azure AD 조건부 액세스를 사용하여 신뢰할 수 있는 장치만 응용 프로그램 프록시를 통해 게시된 API에 액세스할 수 있도록 할 수 있습니다. 데스크톱에 대해 Azure AD 조인 또는 Azure AD 하이브리드 조인을 사용하고 장치에 대해 관리되는 Intune을 사용합니다. 또한 Azure 다단계 인증과 같은 Azure Active Directory Premium 기능 및 Azure [ID 보호의](/azure/active-directory/active-directory-identityprotection)기계 학습 지원 보안을 활용할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 단계를 수행하려면 다음이 필요합니다.

- 앱을 만들고 등록할 수 있는 계정으로 Azure 디렉터리에 대한 관리자 액세스
- 샘플 웹 API 및 네이티브 클라이언트 앱에서[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>응용 프로그램 프록시를 통해 API 게시

응용 프로그램 프록시를 통해 인트라넷 외부에 API를 게시하려면 웹 앱을 게시할 때와 동일한 패턴을 따릅니다. 자세한 내용은 [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)를 참조합니다.

응용 프로그램 프록시를 통해 SecretAPI 웹 API를 게시하려면 다음을 수행하십시오.

1. 샘플 SecretAPI 프로젝트를 로컬 컴퓨터 또는 인트라넷에 ASP.NET 웹 앱으로 빌드하고 게시합니다. 로컬에서 웹 앱에 액세스할 수 있는지 확인합니다. 
   
1. Azure [포털에서](https://portal.azure.com) **Azure Active 디렉터리**를 선택합니다. 그런 다음 **엔터프라이즈 응용 프로그램을 선택합니다.**
   
1. 엔터프라이즈 응용 프로그램 맨 위에 **있는 모든 응용 프로그램** 페이지에서 새 응용 **프로그램을**선택합니다.
   
1. 응용 **프로그램 추가** 페이지에서 **온-프레미스 응용 프로그램을 선택합니다.** **사용자 고유의 온-프레미스 응용 프로그램 추가** 페이지가 나타납니다.
   
1. 응용 프로그램 프록시 커넥터가 설치되어 있지 않으면 설치하라는 메시지가 표시됩니다. 커넥터를 다운로드하고 설치하려면 **응용 프로그램 프록시 커넥터 다운로드를** 선택합니다. 
   
1. 응용 프로그램 프록시 커넥터를 설치한 후 **온-프레미스 응용 프로그램 추가** 페이지에서 다음을 수행합니다.
   
   1. **이름**옆에 *SecretAPI를*입력합니다.
      
   1. 내부 **URL**옆에 인트라넷 내에서 API에 액세스하는 데 사용하는 URL을 입력합니다.
      
   1. 사전 **인증이** **Azure Active Directory로**설정되어 있는지 확인합니다. 
      
   1. 페이지 상단에서 **추가를** 선택하고 앱이 만들어질 때까지 기다립니다.
   
   ![API 앱 추가](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 엔터프라이즈 응용 프로그램 - 모든 응용 프로그램 페이지에서 **SecretAPI** 앱을 **선택합니다.** 
   
1. **SecretAPI - 개요** 페이지에서 왼쪽 탐색에서 **속성을** 선택합니다.
   
1. **MyApps** 패널의 최종 사용자가 API를 사용할 수 없도록 하려면 **속성** 페이지 하단의 **사용자에게 표시를** **없음으로** 설정한 다음 **저장을**선택합니다.
   
   ![사용자에게 표시되지 않음](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Azure AD 응용 프로그램 프록시를 통해 웹 API를 게시했습니다. 이제 앱에 액세스할 수 있는 사용자를 추가합니다. 

1. **SecretAPI - 개요** 페이지에서 왼쪽 탐색에서 **사용자 및 그룹을** 선택합니다.
   
1. 사용자 **및 그룹** 페이지에서 **사용자 추가를**선택합니다.  
   
1. 과제 **추가** 페이지에서 **사용자 및 그룹을**선택합니다. 
   
1. 사용자 및 그룹 페이지에서 최소한 자신을 포함하여 앱에 액세스할 수 있는 사용자를 **검색하고** 선택합니다. 모든 사용자를 선택한 후 **을 선택합니다.** 
   
   ![사용자 선택 및 할당](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. **과제 추가** 페이지에서 다시 **수행을**선택합니다. 

> [!NOTE]
> 통합 Windows 인증을 사용하는 API에는 [추가 단계가](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)필요할 수 있습니다.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>네이티브 앱을 등록하고 API에 대한 액세스 권한을 부여합니다.

네이티브 앱은 특정 플랫폼 또는 장치에서 사용하도록 개발된 프로그램입니다. 네이티브 앱이 API를 연결하고 액세스하려면 먼저 Azure AD에 등록해야 합니다. 다음 단계에서는 네이티브 앱을 등록하고 응용 프로그램 프록시를 통해 게시한 웹 API에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.

AppProxyNativeApp샘플 네이티브 앱을 등록하려면 다음을 수행하십시오.

1. Azure Active Directory **개요** 페이지에서 **앱 등록을**선택하고 **앱 등록** 창 상단에서 새 **등록을**선택합니다.
   
1. 응용 프로그램 등록 페이지에서 **다음을 수행합니다.**
   
   1. **이름**아래에서 *AppProxyNativeAppSample을*입력합니다. 
      
   1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다. 
      
   1. **리디렉션 URL에서**드롭다운하고 **공용 클라이언트(모바일 & 데스크톱)를**선택한 다음 *https를 입력합니다.\/* 
      
   1. **등록을**선택하고 앱이 성공적으로 등록될 때까지 기다립니다. 
      
      ![새 애플리케이션 등록](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
이제 Azure 활성 디렉터리에서 AppProxyNativeAppSample 앱을 등록했습니다. 네이티브 앱에서 SecretAPI 웹 API에 액세스할 수 있도록 하려면 다음을 수행하십시오.

1. Azure Active Directory **개요** > 앱 등록 페이지에서 **AppProxyNativeAppSample** 앱을**선택합니다.** 
   
1. **AppProxyNativeAppSample** 페이지에서 왼쪽 탐색에서 **API 권한을 선택합니다.** 
   
1. API 사용 권한 페이지에서 **권한 추가를** **선택합니다.**
   
1. 첫 번째 API 권한 요청 페이지에서 **조직에서 사용하는 API탭을** 선택한 다음 **SecretAPI**를 검색하고 **선택합니다.** 
   
1. 다음 **API 권한 요청** 페이지에서 **user_impersonation**옆에 있는 확인란을 선택한 다음 **권한 추가를 선택합니다.** 
   
    ![API 선택](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. **API 권한** 페이지에서 **Contoso에 대한 관리자 동의를** 선택하여 다른 사용자가 앱에 개별적으로 동의하지 못하도록 할 수 있습니다. 

## <a name="configure-the-native-app-code"></a>네이티브 앱 코드 구성

마지막 단계는 네이티브 앱을 구성하는 것입니다. NativeClient 샘플 앱의 *Form1.cs* 파일에서 다음 코드 조각은 ADAL 라이브러리가 API 호출을 요청하기 위한 토큰을 획득하고 이를 앱 헤더에 전달자로 연결합니다. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
네이티브 앱을 Azure Active Directory에 연결하고 API 앱 프록시를 호출하도록 구성하려면 NativeClient 샘플 앱의 *App.config* 파일에서 자리 표시자 값을 Azure AD의 값으로 업데이트합니다. 

- 필드에 **디렉터리(테넌트) ID를** 붙여넣습니다. `<add key="ida:Tenant" value="" />` 앱 중 하나의 **개요** 페이지에서 이 값(GUID)을 찾아 복사할 수 있습니다. 
  
- AppProxyNativeAppSample 응용 **프로그램(클라이언트) ID를** 필드에 붙여넣습니다. `<add key="ida:ClientId" value="" />` AppProxyNativeApp **개요** 페이지에서 이 값(GUID)을 찾아 복사할 수 있습니다.
  
- AppProxyNativeAppSample 리디렉션 **URI를** 필드에 `<add key="ida:RedirectUri" value="" />` 붙여넣습니다. AppProxyNativeApp **인증** 페이지에서 이 값(URI)을 찾아 복사할 수 있습니다. 
  
- 필드에 SecretAPI **응용 프로그램 ID URI를** 붙여넣습니다. `<add key="todo:TodoListResourceId" value="" />` SecretAPI 노출 API 페이지에서 이 값(URI)을 찾아 **복사할** 수 있습니다.
  
- 필드에 SecretAPI **홈 페이지 URL을** 붙여넣습니다. `<add key="todo:TodoListBaseAddress" value="" />` SecretAPI **브랜딩** 페이지에서 이 값(URL)을 찾아 복사할 수 있습니다.

매개 변수를 구성한 후 네이티브 앱을 빌드하고 실행합니다. **로그인** 단추를 선택하면 앱에서 로그인한 다음 성공 화면을 표시하여 SecretAPI에 성공적으로 연결되어 있는지 확인할 수 있습니다.

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)
- [빠른 시작: 웹 API에 액세스하도록 클라이언트 응용 프로그램을 구성합니다.](../develop/quickstart-configure-app-access-web-apis.md)
- [네이티브 클라이언트 응용 프로그램이 프록시 응용 프로그램과 상호 작용하도록 설정하는 방법](application-proxy-configure-native-client-application.md)
