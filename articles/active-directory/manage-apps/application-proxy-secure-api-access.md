---
title: 온-프레미스 액세스 Azure AD 응용 프로그램 프록시를 사용 하 여 Api
description: Azure Active Directory 응용 프로그램 프록시 수 안전 하 게 Api에 액세스 하는 네이티브 앱 및 온-프레미스를 호스트 하는 비즈니스 논리 또는 클라우드 Vm.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 47f6678f8d18d734176d964f18a6febecea957ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481416"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 여 온-프레미스 Api에 대 한 보안 액세스

클라우드에서 virtual machines에서 호스트 또는 비즈니스 논리 온-프레미스를 실행 하는 Api를 할 수 있습니다. 네이티브 Android, iOS, Mac 또는 Windows 앱 사용자 상호 작용을 제공 하거나 데이터를 사용 하 여 API 끝점을 사용 하 여 상호 작용 해야 합니다. Azure AD 응용 프로그램 프록시 및 [Azure Active Directory 인증 라이브러리 (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) 네이티브 앱 온-프레미스 Api를 안전 하 게 액세스할 수 있습니다. Azure Active Directory Application Proxy는 방화벽 포트를 열고 인증 및 권한 부여 앱 계층에서 제어 하는 보다 빠르고 보다 안전한 솔루션입니다. 

이 문서에서는 네이티브 앱에 액세스할 수 있는 웹 API 서비스를 호스팅하기 위한 Azure AD 응용 프로그램 프록시 솔루션을 설정 하는 방법을 안내 합니다. 

## <a name="overview"></a>개요

다음 다이어그램에는 온-프레미스 Api 게시 하는 일반적인 방법을 보여 줍니다. 이 방법은 들어오는 포트 80 및 443 열기 해야 합니다.

![기존 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

다음 다이어그램은 Azure AD 응용 프로그램 프록시를 사용 하 여 안전 하 게 들어오는 모든 포트를 열지 않고 Api를 게시 하는 방법을 보여줍니다.

![Azure AD 응용 프로그램 프록시 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 응용 프로그램 프록시 API 액세스에 대 한 공용 끝점으로 작동 하 고 인증 및 권한 부여를 제공 하는 솔루션의 핵심을 형성 합니다. Api를 사용 하 여 다양 한 플랫폼에서에서 액세스할 수 있습니다 합니다 [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) 라이브러리입니다. 

Azure AD 응용 프로그램 프록시 인증 및 권한 부여는 Azure AD에 기반 하므로 신뢰할 수 있는 장치만 응용 프로그램 프록시를 통해 게시 된 Api에 액세스할 수 있도록 Azure AD 조건부 액세스를 사용할 수 있습니다. 사용 하 여 Azure AD 조인 또는 데스크톱에 대 한 Azure AD 하이브리드 가입 및 Intune 관리 장치에 대 한 합니다. Azure Multi-factor Authentication과 같은 Azure Active Directory Premium 기능을 활용 하 고 machine learning 기반 보안을 사용할 수도 있습니다 [Azure Id 보호](/azure/active-directory/active-directory-identityprotection)합니다.

## <a name="prerequisites"></a>필수 조건

이 연습을 수행 하려면 다음을 수행 해야 합니다.

- 를 만들고 앱을 등록할 수 있는 계정으로 Azure 디렉터리에 대 한 관리자 액세스
- 샘플 웹 API 및 네이티브 클라이언트 앱에서 [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>응용 프로그램 프록시를 통해 API를 게시 합니다.

응용 프로그램 프록시를 통해 인트라넷 외부에서 API를 게시 하려면 웹 앱 게시의 경우와 동일한 패턴을 따릅니다. 자세한 내용은 [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md)합니다.

응용 프로그램 프록시를 통해 SecretAPI web API 게시:

1. 빌드 및 로컬 컴퓨터 또는 인트라넷에서 ASP.NET 웹 앱으로 샘플 SecretAPI 프로젝트를 게시 합니다. 로컬로 웹 앱에 액세스할 수 있는지 확인 합니다. 
   
1. 에 [Azure portal](https://portal.azure.com)를 선택 **Azure Active Directory** 왼쪽된 탐색 영역에서 합니다. 그런 다음 합니다 **개요** 페이지에서 **Enterprise 응용 프로그램**합니다.
   
1. 맨 위에 있는 합니다 **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 **새 응용 프로그램**합니다.
   
1. 에 **응용 프로그램 추가** 페이지의 **고유한 응용 프로그램 추가**를 선택 **온-프레미스 응용 프로그램**합니다. 
   
1. 설치 하는 응용 프로그램 프록시 커넥터에 없는 경우 설치를 묻는 메시지가 나타납니다. 선택 **응용 프로그램 프록시 커넥터 다운로드** 를 다운로드 하 여 커넥터를 설치 합니다. 
   
1. 응용 프로그램 프록시 커넥터를 설치한 후 합니다 **고유한 온-프레미스 응용 프로그램을 추가** 페이지:
   
   1. 입력 *SecretAPI* 옆에 **이름**합니다.
      
   1. 옆에 인트라넷 내에서 API에 액세스 하는 데 사용할 URL을 입력 **내부 Url**합니다. 
      
   1. 했는지 **사전 인증** 로 설정 된 **Azure Active Directory**합니다. 
      
   1. 선택 **추가** 만들려는 앱에 대 한 대기 고 페이지 맨 위에 있는 합니다.
   
   ![API 앱 추가](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 에 **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 선택 합니다 **SecretAPI** 앱. 
   
1. 에 **SecretAPI-개요** 페이지에서 **속성** 왼쪽된 탐색 영역에서 합니다.
   
1. 최종 사용자에 게 사용 가능 하도록 Api를 표시 하지 않으려면 합니다 **MyApps** 패널에서 설정 **사용자에 게** 를 **아니요** 맨 아래에 **속성**페이지에서 선택한 후 **저장할**합니다.
   
   ![사용자에 게는 보이지 않음](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Azure AD 응용 프로그램 프록시를 통해 web API를 게시 했습니다. 이제 앱에 액세스할 수 있는 사용자를 추가 합니다. 

1. 에 **SecretAPI-개요** 페이지에서 **사용자 및 그룹** 왼쪽된 탐색 영역에서 합니다.
   
1. 에 **사용자 및 그룹** 페이지에서 **사용자 추가**합니다.  
   
1. 에 **할당 추가** 페이지에서 **사용자 및 그룹**합니다. 
   
1. 에 **사용자 및 그룹** 페이지를 검색 하 고 직접 이상 등 앱에 액세스할 수 있는 사용자를 선택 합니다. 모든 사용자를 선택한 후 선택 **선택**합니다. 
   
   ![선택한 사용자를 할당 합니다.](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 다시 합니다 **할당 추가** 페이지에서 **할당**합니다. 

> [!NOTE]
> 통합 Windows 인증을 사용 하는 Api 필요할 [단계를 추가로](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)입니다.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>네이티브 앱을 등록 하 고 API에 대 한 액세스 부여

네이티브 앱은 특정 플랫폼 또는 장치에서 사용 하기 위해 개발 된 프로그램입니다. 네이티브 앱 연결 하 고 API에 액세스할 수 있습니다, 전에 Azure AD에 등록 해야 합니다. 다음 단계에는 네이티브 앱을 등록 하 고 웹 응용 프로그램 프록시를 통해 게시 된 API에 액세스할 방법을 보여 줍니다.

AppProxyNativeAppSample 네이티브 앱을 등록 합니다.

1. Azure Active directory **개요** 페이지에서 **앱 등록**, 및의 맨 위에 있는 합니다 **앱 등록** 창 **새 등록** .
   
1. 에 **응용 프로그램을 등록** 페이지:
   
   1. 아래 **이름을**를 입력 *AppProxyNativeAppSample*합니다. 
      
   1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다. 
      
   1. 아래 **리디렉션 URL**드롭다운을 선택 **공용 클라이언트 (모바일 및 데스크톱)** 를 입력 한 다음 *https:\//appproxynativeapp*합니다. 
      
   1. 선택 **등록**, 앱을 성공적으로 등록 될 때까지 기다립니다. 
      
      ![새 애플리케이션 등록](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
이제 Azure Active Directory에서 AppProxyNativeAppSample 앱을 등록 했습니다. SecretAPI 웹 API에 네이티브 응용 프로그램 액세스를 제공 합니다.

1. Azure Active directory **개요** > **앱 등록** 페이지를 선택 합니다 **AppProxyNativeAppSample** 앱. 
   
1. 에 **AppProxyNativeAppSample** 페이지에서 **API 사용 권한** 왼쪽된 탐색 영역에서 합니다. 
   
1. 에 **API 사용 권한** 페이지에서 **권한을 추가**합니다.
   
1. 첫 번째 **요청 API 사용 권한** 페이지에서 선택 합니다 **내 조직에서 사용 하는 Api** 탭 한 다음 검색 하 고 선택 **SecretAPI**합니다. 
   
1. 다음에 **요청 API 사용 권한** 페이지 옆에 확인란을 선택 합니다 **user_impersonation**를 선택한 후 **권한 추가**합니다. 
   
    ![API 선택](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 다시를 **API 사용 권한** 페이지를 선택할 수 있습니다 **Contoso에 대 한 관리자 동의 부여** 다른 사용자가 앱에 개별적으로 동의 하지 않아도 하지 못하도록 합니다. 

## <a name="configure-the-native-app-code"></a>네이티브 응용 프로그램 코드 구성

마지막 단계는 네이티브 앱을 구성 하는 것입니다. 다음 코드 조각을 합니다 *Form1.cs* NativeClient 샘플 앱에서 ADAL 라이브러리 API 호출에 요청에 대 한 토큰을 획득 하 고 전달자도 앱 헤더 연결에 발생 합니다. 
   
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
   
Azure Active Directory에 연결 하 여 API 앱 프록시를 호출 하는 네이티브 앱을 구성 하려면에서 자리 표시자 값을 업데이트 합니다 *App.config* Azure AD에서 값을 사용 하 여 NativeClient 샘플 앱의 파일: 

- 붙여넣기 합니다 **디렉터리 (테 넌 트) ID** 에 `<add key="ida:Tenant" value="" />` 필드입니다. 찾아서에서이 값 (GUID)를 복사할 수 있습니다 합니다 **개요** 페이지 앱 중 하나입니다. 
  
- AppProxyNativeAppSample 붙여 **(클라이언트) 응용 프로그램 ID** 에 `<add key="ida:ClientId" value="" />` 필드입니다. 찾아서는 AppProxyNativeAppSample에서이 값 (GUID)를 복사할 수 있습니다 **개요** 페이지입니다.
  
- AppProxyNativeAppSample 붙여 **리디렉션 URI** 에 `<add key="ida:RedirectUri" value="" />` 필드입니다. 찾아서는 AppProxyNativeAppSample에서이 값 (URI)를 복사할 수 있습니다 **인증** 페이지입니다. 
  
- SecretAPI 붙여 **응용 프로그램 ID URI** 에 `<add key="todo:TodoListResourceId" value="" />` 필드입니다. 찾아서는 SecretAPI에서이 값 (URI)를 복사할 수 있습니다 **API를 노출** 페이지입니다.
  
- SecretAPI 붙여 **홈 페이지 URL** 에 `<add key="todo:TodoListBaseAddress" value="" />` 필드입니다. 찾아서는 SecretAPI에서이 값 (URL)를 복사할 수 있습니다 **브랜드** 페이지입니다.

매개 변수를 구성한 후에 빌드 및 네이티브 앱을 실행 합니다. 선택 하는 경우는 **로그인** 단추를 앱에 로그인 하 고 있습니다 표시 한다는 성공적으로 확인 하는 성공 화면에 연결 된 SecretAPI 합니다.

![성공](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md)
- [빠른 시작: 웹 Api에 액세스 하는 클라이언트 응용 프로그램 구성](../develop/quickstart-configure-app-access-web-apis.md)
- [네이티브 클라이언트 응용 프로그램 프록시 응용 프로그램과 상호 작용을 사용 하는 방법](application-proxy-configure-native-client-application.md)
