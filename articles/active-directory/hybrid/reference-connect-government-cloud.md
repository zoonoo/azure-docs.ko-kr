---
title: 'Azure AD Connect: Azure Government 클라우드에 대한 하이브리드 ID 고려 사항'
description: Azure Government 클라우드를 사용하여 Azure AD Connect를 배포할 때 특별히 고려해야 할 사항입니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219893859d05eb419bc862484a9083abf8c26db7
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409300"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Government 클라우드에 대한 하이브리드 ID 고려 사항

이 문서에서는 하이브리드 환경을 Microsoft Azure Government 클라우드와 통합하기 위한 고려 사항을 설명합니다. 이 정보는 Azure Government 클라우드를 사용하는 관리자와 설계자를 위한 참조로 제공됩니다.

> [!NOTE]
> 온-프레미스 Microsoft Azure AD(Azure Active Directory) 환경을 Azure Government 클라우드와 통합하려면 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)의 최신 릴리스로 업그레이드해야 합니다.

미국 Department of Defense(국방부) 엔드포인트의 전체 목록은 [설명서](/office365/enterprise/office-365-u-s-government-dod-endpoints)를 참조하세요.

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 통과 인증

다음 정보는 통과 인증 및 Azure Government 클라우드의 구현에 대해 설명합니다.

### <a name="allow-access-to-urls"></a>URL에 대한 액세스 허용

통과 인증 에이전트를 배포하기 전에 서버와 Azure AD 사이에 방화벽이 있는지 확인합니다. 방화벽 또는 프록시에서 DNS(Domain Name System) 차단 또는 안전 프로그램을 허용하는 경우 다음 연결을 추가합니다.

> [!NOTE]
> 다음 지침은 Azure Government 환경에 대해 [Azure AD 애플리케이션 프록시 커넥터](../manage-apps/what-is-application-proxy.md)를 설치하는 경우에도 적용됩니다.

|URL |사용 방법|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|에이전트는 다음과 같은 URL을 사용하여 Azure AD 클라우드 서비스와 통신합니다. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| 에이전트는 다음과 같은 URL을 사용하여 인증서를 확인합니다.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| 에이전트는 등록 프로세스 동안 다음과 같은 URL을 사용합니다.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure Government 클라우드에 대한 에이전트를 설치합니다.

Azure Government 클라우드에 대한 에이전트를 설치하려면 다음 단계를 따르세요.

1. 명령줄 터미널에서 에이전트를 설치하는 실행 파일이 포함된 폴더로 이동합니다.
1. Azure Government에 대한 설치를 지정하는 다음 명령을 실행합니다.

   통과 인증의 경우:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   애플리케이션 프록시의 경우:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>SSO(Single sign-on)

### <a name="set-up-your-azure-ad-connect-server"></a>Azure AD Connect 서버 설정

로그인 방법으로 통과 인증을 사용하는 경우 추가 필수 구성 요소 확인이 필요하지 않습니다. 로그인 방법으로 암호 해시 동기화를 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.

- Azure AD Connect 버전 1.1.644.0 이상을 사용합니다.
- 방화벽 또는 프록시에서 DNS 차단 또는 안전 프로그램을 허용하는 경우 포트 443을 통한 *.msappproxy.us URL에 대한 연결을 추가합니다.

  그렇지 않으면 매주 업데이트되는 Azure 데이터 센터 IP 범위에 액세스하도록 허용합니다. 이 필수 조건은 해당 기능을 사용하도록 설정한 경우에만 적용됩니다. 실제 사용자 로그인에서는 필요하지 않습니다.

### <a name="roll-out-seamless-single-sign-on"></a>원활한 Single Sign-On 롤아웃

다음 지침을 사용하여 Azure AD 원활한 Single Sign-On을 사용자에게 점진적으로 롤아웃할 수 있습니다. Active Directory의 그룹 정책을 사용하여 모든 또는 선택된 사용자의 인트라넷 영역 설정에 Azure AD URL `https://autologon.microsoft.us`를 추가하기 시작합니다.

또한 **그룹 정책으로 스크립트를 통해 상태 표시줄에 대한 업데이트 허용** 이라는 인트라넷 영역 정책 설정을 사용하도록 설정해야 합니다.

## <a name="browser-considerations"></a>브라우저 고려 사항

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox(모든 플랫폼)

Mozilla Firefox는 Kerberos 인증을 자동으로 사용하지 않습니다. 각 사용자는 다음 단계에 따라 Firefox 설정에 Azure AD URL을 수동으로 추가해야 합니다.

1. Firefox를 실행하고 주소 표시줄에  **about:config** 를 입력합니다. 표시되는 모든 알림을 해제합니다.
1.  **network.negotiate-auth.trusted-uris**  기본 설정을 검색합니다. 이 기본 설정은 Kerberos 인증을 위한 Firefox의 신뢰할 수 있는 사이트를 나열합니다.
1. 기본 설정 이름을 마우스 오른쪽 단추로 클릭한 다음,  **수정** 을 선택합니다.
1. 상자에 `https://autologon.microsoft.us`를 입력합니다.
1.  **확인** 을 선택한 다음, 브라우저를 다시 엽니다.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium 기반 Microsoft Edge(모든 플랫폼)

사용자 환경에서  `AuthNegotiateDelegateAllowlist`  또는 `AuthServerAllowlist`  정책 설정을 재정의한 경우 Azure AD URL `https://autologon.microsoft.us`를 추가해야 합니다.

### <a name="google-chrome-all-platforms"></a>Google Chrome(모든 플랫폼)

사용자 환경에서  `AuthNegotiateDelegateWhitelist`  또는 `AuthServerWhitelist`  정책 설정을 재정의한 경우 Azure AD URL `https://autologon.microsoft.us`를 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [통과 인증](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-On](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)