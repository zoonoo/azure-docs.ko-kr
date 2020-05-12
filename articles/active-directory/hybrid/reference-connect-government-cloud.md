---
title: 'Azure AD Connect: Azure Government 클라우드의 하이브리드 id 고려 사항'
description: Azure Government 클라우드를 사용 하 여 Azure AD Connect를 배포할 때 특별히 고려해 야 할 사항입니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115492"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Government 클라우드의 하이브리드 id 고려 사항

이 문서에서는 하이브리드 환경을 Microsoft Azure Government 클라우드와 통합 하기 위한 고려 사항을 설명 합니다. 이 정보는 Azure Government 클라우드를 사용 하는 관리자와 설계자를 위한 참조로 제공 됩니다.

> [!NOTE]
> 온-프레미스 Microsoft Azure Active Directory (Azure AD) 환경을 Azure Government 클라우드와 통합 하려면 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)최신 릴리스로 업그레이드 해야 합니다.

방어 끝점의 미국 정부 부서에 대 한 전체 목록은 [설명서](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)를 참조 하세요.

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 통과 인증

다음 정보는 통과 인증 및 Azure Government 클라우드의 구현에 대해 설명 합니다.

### <a name="allow-access-to-urls"></a>URL에 대한 액세스 허용

통과 인증 에이전트를 배포 하기 전에 서버와 Azure AD 사이에 방화벽이 있는지 확인 합니다. 방화벽 또는 프록시에서 DNS (Domain Name System) 차단 또는 안전 프로그램을 허용 하는 경우 다음 연결을 추가 합니다.

> [!NOTE]
> 다음 지침은 Azure Government 환경용 [Azure AD 응용 프로그램 프록시 커넥터](https://aka.ms/whyappproxy) 를 설치 하는 경우에도 적용 됩니다.

|URL |사용 방법|
|-----|-----|
|&#42; msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|에이전트는 이러한 Url을 사용 하 여 Azure AD 클라우드 서비스와 통신 합니다. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| 에이전트는 이러한 Url을 사용 하 여 인증서를 확인 합니다.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42; microsoftonline.us </br>&#42; microsoftonline-p.us </br>&#42; msauth.net </br>&#42; msauthimages.net </br>&#42; msecnd.net</br>&#42; msftauth.net </br>&#42; msftauthimages.net</br>&#42; phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 에이전트는 등록 프로세스 중에 이러한 Url을 사용 합니다.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure Government 클라우드에 대 한 에이전트 설치

Azure Government 클라우드에 대 한 에이전트를 설치 하려면 다음 단계를 따르세요.

1. 명령줄 터미널에서 에이전트를 설치 하는 실행 파일이 포함 된 폴더로 이동 합니다.
1. Azure Government에 대 한 설치를 지정 하는 다음 명령을 실행 합니다.

   통과 인증의 경우:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   응용 프로그램 프록시:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>SSO(Single sign-on)

### <a name="set-up-your-azure-ad-connect-server"></a>Azure AD Connect 서버 설정

통과 인증을 로그온 방법으로 사용 하는 경우 추가 필수 구성 요소 확인이 필요 하지 않습니다. 로그인 방법으로 암호 해시 동기화를 사용 하 고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인 합니다.

- Azure AD Connect 버전 1.1.644.0 이상을 이상을 사용 합니다.
- 방화벽 또는 프록시에서 DNS 차단 또는 안전 프로그램을 허용 하는 경우 443 포트를 통해 &#42; msappproxy.us Url에 연결을 추가 합니다.

  그렇지 않으면 매주 업데이트 되는 Azure 데이터 센터 IP 범위에 대 한 액세스를 허용 합니다. 이 필수 구성 요소는 기능을 사용 하도록 설정한 경우에만 적용 됩니다. 실제 사용자 로그인에는이 기능이 필요 하지 않습니다.

### <a name="roll-out-seamless-single-sign-on"></a>원활한 Single Sign-on 롤아웃

다음 지침을 사용 하 여 Azure AD 원활한 Single Sign-on을 사용자에 게 점진적으로 롤아웃할 수 있습니다. [https://autologon.microsoft.us](https://autologon.microsoft.us)Active Directory에서 그룹 정책를 사용 하 여 전체 또는 선택한 사용자의 인트라넷 영역 설정에 AZURE AD URL을 추가 하 여 시작 합니다.

또한 **그룹 정책를 통해 스크립트를 통해 상태 표시줄에 대 한 업데이트 허용**과 같은 인트라넷 영역 정책 설정을 사용 하도록 설정 해야 합니다.

## <a name="browser-considerations"></a>브라우저 고려 사항

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox(모든 플랫폼)

Mozilla Firefox는 Kerberos 인증을 자동으로 사용하지 않습니다. 각 사용자는 다음 단계에 따라 Firefox 설정에 Azure AD URL을 수동으로 추가 해야 합니다.

1. Firefox를 실행 하 고 주소 표시줄에 **about: config**를 입력   합니다. 표시 될 수 있는 모든 알림을 해제 합니다.
1. 네트워크를 검색 **합니다. negotiate-auth. 신뢰할 수 있는 uri**   기본 설정입니다. 이 기본 설정은 Firefox에서 Kerberos 인증에 대해 신뢰할 수 있는 사이트를 나열 합니다.
1. 기본 설정 이름을 마우스 오른쪽 단추로 클릭 한 다음 **수정**을 선택 합니다.
1.  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   상자에을 입력 합니다.
1.  **확인**을 선택   하 고 브라우저를 다시 엽니다.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium 기반 Microsoft Edge (모든 플랫폼)

 `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   사용자 환경에서 또는 정책 설정을 재정의 한 경우 Azure AD URL을 추가 했는지 확인 [https://autologon.microsoft.us](https://autologon.microsoft.us) 합니다.

### <a name="google-chrome-all-platforms"></a>Google Chrome(모든 플랫폼)

 `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   사용자 환경에서 또는 정책 설정을 재정의 한 경우 Azure AD URL을 추가 했는지 확인 [https://autologon.microsoft.us](https://autologon.microsoft.us) 합니다.

## <a name="next-steps"></a>다음 단계

- [통과 인증](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
