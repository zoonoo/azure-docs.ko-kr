---
title: 'Azure AD 연결: Azure 정부에 대 한 하이브리드 ID 고려 사항'
description: Azure AD Connect를 정부 클라우드에 배포하기 위한 특별한 고려 사항입니다.
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378926"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Azure 정부에 대한 하이브리드 ID 고려 사항 
다음 문서에서는 Azure 정부 클라우드를 통해 하이브리드 환경을 구현하기 위한 고려 사항에 대해 설명합니다.  이 정보는 Azure 정부 클라우드로 작업하는 관리자 및 설계자에 대한 참조로 제공됩니다.  
> [!NOTE] 
> 온-프레미스 AD 환경을 Azure Governemnt 클라우드와 통합하려면 [Azure AD Connect의](https://www.microsoft.com/download/details.aspx?id=47594)최신 릴리스로 업그레이드해야 합니다. 

> [!NOTE] 
> 미국 정부 DoD 엔드포인트의 전체 목록은 [문서를](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 참조하십시오. 

## <a name="pass-through-authentication"></a>통과 인증 
PTA(통과 인증) 및 Azure 정부 클라우드구현을 위해 다음 정보가 제공됩니다.

### <a name="allow-access-to-urls"></a>URL에 대한 액세스 허용  
통과 인증 에이전트를 배포하기 전에 서버와 Azure AD 사이에 방화벽이 있는지 확인합니다. 방화벽 또는 프록시에서 DNS 허용 목록을 허용하는 경우 다음 연결을 추가합니다. 
> [!NOTE]
> 다음 지침은 Azure 정부 환경에 대한 [응용 프로그램 프록시 커넥터](https://aka.ms/whyappproxy) 설치에도 적용됩니다.

|URL |사용 방법|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|에이전트와 Azure AD 클라우드 서비스 간의 통신 |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| 에이전트는 이러한 URL을 사용하여 인증서를 확인합니다.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br> *.microsoftonline-p.us </br>*msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 에이전트는 등록 프로세스 중에 이러한 URL을 사용합니다.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure 정부 클라우드에 대한 에이전트 설치 
Azure 정부 클라우드에 대한 에이전트를 설치하려면 명령줄 터미널에서 에이전트 설치를 위한 실행 프로그램이 있는 폴더로 이동합니다. 설치를 지정 하는 다음 명령을 실행 Azure 정부에 대 한 입니다. 

통과 인증의 경우: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

응용 프로그램 프록시의 경우:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Single Sign-On 
Azure AD Connect 서버 설정: 로그인 방법으로 통과 인증을 사용하는 경우 추가 필수 구성 조건 검사가 필요하지 않습니다. 암호 해시 동기화를 로그인 메서드로 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 수행하십시오.
- Azure AD Connect 버전 1.1.644.0 이상을 사용합니다. 
- 방화벽 또는 프록시에서 DNS 허용 목록을 허용하는 경우 포트 443을 통해 *.msapproxy.us URL에 연결을 추가합니다. 그렇지 않은 경우 매주 업데이트되는 Azure 데이터 센터 IP 범위에 대한 액세스를 허용합니다. 이 필수 조건은 해당 기능을 사용하도록 설정한 경우에만 적용할 수 있습니다. 실제 사용자 로그인에서는 필요하지 않습니다. 

### <a name="rolling-out-seamless-sso"></a>원활한 SSO 출시 
아래 제공된 지침을 사용하여 사용자에게 원활한 SSO를 점진적으로 롤아웃할 수 있습니다. 먼저 Active Directory에서 그룹 정책을 사용하여 모든 사용자의 인트라넷 영역 설정에 다음 Azure AD URL을 추가합니다.https://autologon.microsoft.us 

또한 그룹 정책을 통해 스크립트를 통해 상태 표시줄에 대한 업데이트 허용이라는 인트라넷 영역 정책 설정을 사용하도록 설정해야 합니다. 브라우저 고려 모질라 파이어 폭스 (모든 플랫폼) 모질라 파이어 폭스는 자동으로 Kerberos 인증을 사용하지 않습니다. 각 사용자는 다음 단계에 따라 Firefox 설정에 Azure AD URL을 수동으로 추가해야 합니다. 
1. 파이어 폭스를 실행하고 주소 표시 줄에 약 :config를 입력합니다. 표시되는 모든 알림을 해제합니다. 
2. network.negotiate-auth.trusted-uris 환경 설정을 검색합니다. 이 기본 설정은 Firefox의 신뢰할 수 있는 Kerberos 인증 사이트를 나열합니다. 
3. 마우스 오른쪽 단추를 클릭하고 수정을 선택합니다. 
4. 필드에 https://autologon.microsoft.us 입력합니다.
5. 확인을 선택한 다음 브라우저를 다시 엽니다. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>크롬 (모든 플랫폼)을 기반으로 마이크로 소프트 에지 
 `AuthNegotiateDelegateAllowlist`  `AuthServerAllowlist`환경의 정책 설정 또는 재정의한 경우 Azure AD의 URL(해당https://autologon.microsoft.us) URL)도 추가해야 합니다. 

### <a name="google-chrome-all-platforms"></a>Google Chrome(모든 플랫폼) 
 `AuthNegotiateDelegateWhitelist`  `AuthServerWhitelist`환경의 정책 설정 또는 재정의한 경우 Azure AD의 URL(해당https://autologon.microsoft.us) URL)도 추가해야 합니다. 

## <a name="next-steps"></a>다음 단계
[통과 인증](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[단일 사인온](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
