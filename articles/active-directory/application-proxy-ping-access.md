---
title: "Azure AD 응용 프로그램 프록시용 PingAccess | Microsoft Docs"
description: "PingAccess 및 앱 프록시를 사용하여 응용 프로그램을 게시하여 헤더 기반 인증을 지원합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 173607c481d0ba7ceece6310fcd131ff622a0677
ms.lasthandoff: 03/23/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-pingaccess-for-azure-ad"></a>Azure AD용 PingAccess와 함께 헤더 기반 인증을 지원하는 응용 프로그램 게시

Azure Active Directory 응용 프로그램 프록시 및 PingAccess는 Azure Active Directory 고객에게 더 많은 응용 프로그램에 대한 액세스를 제공하도록 파트너 관계를 맺고 있습니다. PingAccess는 인증에 헤더를 사용하는 응용 프로그램에 대한 원격 액세스를 포함하도록 [기존 응용 프로그램 프록시 제품](active-directory-application-proxy-get-started.md)을 확장합니다. 

## <a name="what-is-pingaccess-for-azure-ad"></a>Azure AD용 PingAccess는 무엇입니까?

사용자에게 인증에 헤더를 사용하는 앱에 대한 액세스 권한을 부여하려면 응용 프로그램 프록시 및 PingAccess 모두에서 원격 액세스를 위한 앱을 게시합니다. 응용 프로그램 프록시는 Azure AD를 사용하여 액세스를 인증한 다음 커넥터 서비스를 통해 트래픽을 전달하여 다른 앱과 같이 이러한 앱을 처리합니다. PingAccess는 응용 프로그램에서 읽을 수 있는 형식으로 인증을 받도록 앱 앞에 위치하고 Azure AD의 액세스 토큰을 헤더로 변환합니다. 

사용자는 회사 앱을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 장치에서 작업할 수 있습니다. 사용자가 사무실에 있는 경우 응용 프로그램 프록시와 PingAccess 둘다 트래픽을 가로채는 일이 없으므로 사용자는 평소와 동일한 환경을 사용하게 됩니다.

응용 프로그램 프록시 커넥터는 해당 인증 유형에 관계 없이 모든 앱에 원격 트래픽을 보내므로 계속해서 자동으로 부하 균형을 유지합니다. 

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory 및 PingAccess 간의 파트너 관계를 통해 제공되므로 두 서비스에 대한 라이선스가 필요합니다. Azure Active Directory Premium 구독은 이 흐름과 함께 최대 20개의 응용 프로그램을 구성할 수 있도록 시작 PingAccess 라이선스와 함께 제공됩니다. 

자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

## <a name="publish-your-first-application"></a>첫 번째 응용 프로그램 게시

이 문서는 처음으로 이 시나리오를 사용하여 앱을 게시하는 사람들을 위한 것입니다. 게시 단계 외에도 응용 프로그램과 PingAccess를 시작하는 방법을 안내합니다. 두 서비스를 이미 구성했지만 게시 단계에서 리프레셔를 원하는 경우 두 등록 섹션을 건너뛸 수 있습니다.

>[!NOTE]
>이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다. 

### <a name="install-an-application-proxy-connector"></a>응용 프로그램 프록시 커넥터 설치

응용 프로그램 프록시를 이미 활성화하고 커넥터를 설치한 경우 해당 단계를 건너뛸 수 있습니다.

응용 프로그램 프록시 커넥터는 원격 직원의 트래픽을 게시된 앱으로 전달하는 Windows Server 서비스입니다. 자세한 설치 지침은 [Azure Portal에서 응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md)을 참조하세요.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** > **응용 프로그램 프록시**를 선택합니다.
3. **커넥터 다운로드**를 선택하여 응용 프로그램 프록시 커넥터 다운로드를 시작합니다. 설치 지침을 따릅니다. 
4. 커넥터를 다운로드하면 디렉터리에 대해 응용 프로그램 프록시가 자동으로 활성화되지만 그렇지 않은 경우 **응용 프로그램 프록시 사용**을 선택할 수 있습니다. 

![응용 프로그램 프록시를 사용하도록 설정하고 커넥터 다운로드](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>응용 프로그램 프록시를 사용하여 Azure AD에 앱 추가

이 섹션은 두 부분으로 분류됩니다. 먼저 Azure AD에 앱을 게시해야 합니다. 그런 다음 PingAccess 단계 동안 사용할 수 있는 해당 앱에 대한 정보를 수집해야 합니다. 

#### <a name="publish-the-app"></a>앱 게시

1. 마지막 섹션에 있지 않았던 경우 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** > **Enterprise 응용 프로그램**을 선택합니다. 
3. 블레이드의 위쪽에서 **추가**를 선택합니다. 
4. **온-프레미스 응용 프로그램**을 선택합니다.
5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다.
  - **내부 URL**: 회사 네트워크에 있을 때 일반적으로 앱의 로그인 페이지로 안내하는 URL을 제공합니다. 이 파트너 관계에서 커넥터는 PingAccess 프록시를 앱의 첫 페이지로 취급해야 합니다. `https://<host name of your PA server>:<port>/<App path name>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.
  - **사전 인증 방법**: Azure Active Directory
  - **헤더에서 URL 변환**: 아니요
6. 블레이드의 아래쪽에서 **추가**를 선택합니다. 응용 프로그램이 추가되고 빠른 시작 메뉴가 열립니다. 
7. 빠른 시작 메뉴에서 **테스트할 사용자 지정**을 선택하고 응용 프로그램에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 응용 프로그램에 대한 액세스 권한이 있는지 확인합니다. 
8. **할당**을 선택하여 테스트 사용자 할당을 저장합니다. 
9. 앱 관리 블레이드에서 **Single Sign-On**을 선택합니다. 
10. 드롭다운 메뉴에서 **헤더 기반 로그온**을 선택합니다. **저장**을 선택합니다.

  ![헤더 기반 로그온 선택](./media/application-proxy-ping-access/sso-header.PNG)

11. Enterprise 응용 프로그램 블레이드를 닫거나 왼쪽으로 스크롤하여 Azure Active Directory 메뉴로 돌아갑니다. 
12. **앱 등록**을 선택합니다.
13. 방금 추가한 앱을 선택한 다음 **회신 URL**을 선택합니다. 
14. 5단계에서 앱에 할당한 외부 URL이 회신 URL 목록에 있는지 확인합니다. 그렇지 않은 경우 지금 추가합니다. 
15. 앱 설정 블레이드에서 **필요한 권한**을 선택합니다. 
16. **추가**를 선택합니다. API의 경우 **Windows Azure Active Directory**를 선택한 다음 **선택**을 선택합니다. 사용 권한의 경우 **모든 응용 프로그램 읽기 및 작성**을 선택한 다음 **로그인 및 사용자 프로필 읽기**, **선택** 및 **완료**를 차례로 선택합니다.  

  ![권한 선택](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

1. 계속해서 앱 설정 블레이드에서 **속성**을 선택합니다. **응용 프로그램 ID** 값을 저장합니다. 이 값은 PingAccess를 구성할 때 클라이언트 ID에 대해 사용됩니다.
2. 앱 설정 블레이드에서 **키**를 선택합니다. 
3. 키 설명을 입력하고 드롭다운 메뉴에서 만료 날짜를 선택하여 키를 만듭니다. 
4. **저장**을 선택합니다. **값** 필드에 GUID가 나타납니다. 

  이 창을 닫은 후 이 값을 다시 볼 수 없으므로 지금 저장합니다. 

5. 앱 등록 블레이드를 닫거나 왼쪽으로 스크롤하여 Azure Active Directory 메뉴로 돌아갑니다.
6. **속성**을 선택합니다.
7. **디렉터리 ID** GUID를 저장합니다. 

### <a name="download-pingaccess-and-configure-your-app"></a>PingAccess 다운로드 및 앱 구성

이 시나리오의 PingAccess 부분에 대한 자세한 단계는 Ping ID 문서, [Azure AD용 PingAccess 구성](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)에서 계속됩니다.

이러한 단계는 PingAccess 계정이 아직 없는 경우 PingAccess 계정을 얻고, PingAccess 서버를 설치하고, Azure Portal에서 복사한 디렉터리 ID로 Azure AD OIDC 공급자 연결을 만드는 과정을 안내합니다. 그런 다음 응용 프로그램 ID 및 키 값을 사용하여 PingAccess에서 웹 세션을 만듭니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 응용 프로그램을 만들 수 있습니다.

### <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 테스트하려면 브라우저를 열고 Azure에 앱을 게시할 때 만든 외부 URL로 이동합니다. 앱에 할당한 테스트 계정으로 로그인합니다. 

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 프록시 문제 해결](active-directory-application-proxy-troubleshoot.md)

