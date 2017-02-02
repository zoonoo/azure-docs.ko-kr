---
title: 'Azure AD Connect: Single Sign-on | Microsoft Docs'
description: "이 항목은 Single Sign-On이 온-프레미스 Active Directory(AD), 클라우드 기반 Azure Active Directory(Azure AD) 및 연결된 서비스에서 작동하는 방식에 대해 알아야 하는 정보를 제공합니다."
services: active-directory
keywords: "Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: bddd581b5164b03ccba75370cd89a7831101cf37
ms.openlocfilehash: f0133aaf735533159d72b1e63b952258707ad8b9


---

# <a name="what-is-single-sign-on-sso-preview"></a>SSO(Single Sign-On)의 정의(미리 보기)
Single Sign-On은 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-synchronization.md) 또는 [통과 인증](active-directory-aadconnect-pass-through-authentication.md)을 통해 Azure Active Directory Connect에서 활성화할 수 있는 옵션입니다.  활성화되면 회사 네트워크에 연결된 회사 컴퓨터에서 Azure AD(Azure Active Directory) 또는 기타 클라우드 서비스에 로그인하기 위해 사용자가 암호를 입력할 필요가 없습니다.  

Single Sign-On을 활성화하면 사용자가 Azure Active Directory 서비스(예: Office 365, Microsoft Intune, CRM Online 및 SaaS 서비스)에 연결된 리소스에 액세스할 때 암호를 입력할 필요가 없으며 대신 온-프레미스 Active Directory에서 자동으로 받은 Kerberos 티켓이 사용됩니다.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso1.png)

최종 사용자에게 SSO를 제공하면 클라우드 기반 서비스에 대한 액세스가 보다 친숙해지며 추가적인 온-프레미스 구성 요소가 필요 없는 간단하고 안전한 프로세스를 조직에 제공할 수 있습니다.

SSO는 AAD Connect를 통해 활성화되고 암호 해시 동기화 또는 통과 인증 및 온-프레미스 Active Directory와 작동하는 기능입니다.  최종 사용자가 환경 내에서 Single Sign-On을 사용하려면 사용자에 대해 다음과 같은 내용이 확인되어야 합니다.


- 도메인에 연결된 컴퓨터 사용
- 회사 유선 또는 무선 네트워크 또는 VPN 연결과 같은 원격 액세스 연결을 통해 도메인 컨트롤러와 직접 연결
- 클라우드의 Kerberos 끝점을 인트라넷 영역의 일부로 정의

위의 항목이 하나라도 확인되지 않는 경우에는(예: 컴퓨터가 회사 네트워크에 연결되어 있지 않고 Active Directory를 사용할 수 없는 경우) Single Sign-On을 사용하지 않고 로그인해야 하기 때문에 암호를 입력하라는 메시지가 사용자에게 표시됩니다.

## <a name="supported-clients"></a>지원되는 클라이언트
Single Sign-On은 Windows와 같이 Kerberos 인증이 가능한 컴퓨터에서 최신 인증을 지원하는 웹 브라우저 기반 클라이언트 및 Office 클라이언트를 통해 지원됩니다.  아래 행렬은 다양한 운영 체제의 브라우저 기반 클라이언트에 대한 세부 정보를 제공합니다.

| OS\Browser |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- |--- |
|Windows 10|예|예|예*|아니요
|Windows 8.1|예|예|예*|해당 없음
|Windows 8|예|예|예*|해당 없음
|Windows 7|예|예|예*|해당 없음
|Mac|해당 없음|해당 없음|해당 없음|해당 없음

*별도 구성이 필요합니다.

>[!NOTE]
>Windows 10 기반 클라이언트의 경우 최상의 Azure AD 환경을 위해 [Azure AD 조인](../active-directory-azureadjoin-overview.md)을 사용할 것을 권장합니다. 

## <a name="how-single-sign-on-works"></a>Single Sign On 작동 방식

Azure AD Connect에서 Single Sign-On을 활성화하면 온-프레미스 Active Directory에 AZUREADSSOACCT라는 컴퓨터 계정이 생성되고 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다.  또한 클라이언트와 Azure AD 간의 인증 과정에 사용되는 클라우드 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 주체 이름)이 생성됩니다.  

이 설치가 완료되면 인증 프로세스는 다른 모든 IWA(Windows 통합 인증) 기반 응용 프로그램과 동일합니다.  IWA 작동 방식에 익숙하다면 Single Sign-On이 Azure AD와 작동하는 방식도 친숙할 것입니다.  익숙하지 않은 경우, 아래에서 IWA 프로세스를 참조하세요.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso2.png)

먼저 사용자가 리소스에 액세스를 시도합니다.  리소스는 컴퓨터 또는 URL일 수 있습니다.  Azure AD의 경우 리소스는 아래에 “Azure AD 리소스”라고 설명되어 있는 SharePoint Online과 같은 서비스의 URL입니다.

1.  Azure AD 리소스는 Kerberos 티켓을 제공하기 위해 401 권한 없음 응답을 통해 클라이언트에 통신을 시도합니다.
2.  클라이언트는 Azure AD 리소스에 대한 Active Directory의 티켓을 요청합니다. 
3.  Active Directory는 Azure AD 리소스와 연결된 컴퓨터 계정을 찾아서 컴퓨터 계정의 비밀로 암호화된 Kerberos 티켓을 클라이언트에 반환합니다. 티켓에는 컴퓨터에 현재 로그인한 사용자의 ID가 포함됩니다.
4.  클라이언트는 Active Directory에서 받은 Kerberos 티켓을 Azure AD에 보냅니다.
5.  Azure AD는 이전에 공유된 키를 사용하여 Kerberos 티켓의 암호를 해독한 다음 리소스의 요청에 따라 사용자에게 토큰을 반환하거나 사용자에게 Multi-Factor Authentication과 같은 추가적인 증명을 제공하도록 요청합니다.

Single Sign-On은 편의적인 기능입니다. 즉 Single Sign-On이 어떤 이유로 실패하더라도 사용자는 평상시와 같이 로그인 페이지에 암호를 입력하기만 하면 됩니다.

## <a name="enabling-sso-with-pass-through-authentication-or-password-hash-sync"></a>통과 인증 또는 암호 해시 동기화를 통해 SSO 활성화
Azure AD Connect는 통과 인증 또는 암호 해시 동기화를 통해 Single Sign-On을 활성화할 수 있는 간단한 프로세스를 제공합니다.  컴퓨터 계정에서 Kerberos SPN(서비스 주체 이름)을 구성할 수 있으려면 동기화하는 각 포리스트에 포함된 도메인 중 하나에 대해 도메인 관리자 권한이 있는지 확인해야 합니다.  사용자 이름 및 암호는 Azure AD Connect 또는 Azure AD에 저장되지 않으며 이 작업용으로만 사용됩니다.

Azure AD Connect를 설치할 때 사용자 로그인 페이지에서 Single Sign-On을 구성할 수 있도록 사용자 지정 설치를 선택합니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso3.png)

Single Sign-On이 활성화되면 Single Sign-On 페이지가 열릴 때까지 마법사를 계속 진행합니다.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso4.png)

나열되는 각각의 포리스트에 대해 적합한 계정 세부 정보를 제공하면 AZURE 디렉터리에 대해 Single Sign-On이 활성화됩니다.

## <a name="ensuring-clients-sign-in-automatically"></a>클라이언트 로그인 자동으로 확인
기본적으로 브라우저는 인트라넷 영역에서와 같이 URL이 정의되어 있지 않으면 웹 서버에 자격 증명을 보내려는 시도를 하지 않습니다.  일반적으로 브라우저는 URL을 보면 적합한 영역을 계산할 수 있습니다.  예를 들어 URL이 http://intranet/이면 URL이 인트라넷 영역으로 매핑되기 때문에 브라우저는 자격 증명을 자동으로 보냅니다.  하지만 URL에 http://intranet.contoso.com/과 같이 마침표가 포함되어 있으면 서버는 자격 증명을 자동으로 보내지 않고 다른 인터넷 사이트를 처리하듯 URL을 처리합니다.

Azure AD에서 Single Sign-On에 사용되는 URL은 전역 라우팅이 가능한 호스트 이름이라서 마침표를 포함하기 때문에 브라우저가 현재 로그인한 사용자의 자격 증명을 Kerberos 티켓의 형식으로 Azure AD에 자동으로 보내도록 컴퓨터의 인트라넷 영역에 명시적으로 추가되어야 합니다.  필요한 URL을 인트라넷 영역에 추가하는 가장 쉬운 방법은 Active Directory에 그룹 정책을 생성하는 것입니다.

1.  그룹 정책 관리 도구를 엽니다.
2.  모든 사용자에게 표시될 그룹 정책을 편집합니다.  예를 들어 기본 도메인 정책은 다음과 같습니다.
3.  **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page**로 이동하고 **영역에 대한 사이트 할당 목록**을 선택합니다.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png) </br>
4.  정책을 사용하도록 설정하고 대화 상자에서 다음 값/데이터를 입력합니다.</br>
        
        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net 
        Data: 1'
5.  다음과 유사하게 나타납니다. ![Single Sign-On](./media/active-directory-aadconnect-sso/sso7.png)

6.  확인을 클릭하고 확인을 다시 클릭합니다.

이제 사용자가 Single Sign-On을 사용할 준비가 되었습니다.

>[!NOTE]
>기본적으로 Chrome은 Internet Explorer와 동일한 신뢰할 수 있는 사이트 URL 집합을 사용합니다.  Chrome에 대해 다른 설정을 구성한 경우에는 설정을 별도로 업데이트해야 합니다.

## <a name="troubleshooting-single-sign-on-issues"></a>Single Sign-On 문제 해결
다음과 같이 클라이언트가 Single Sign-On을 사용하도록 올바르게 구성되어 있는지 확인하는 것이 중요합니다.

1.  https://autologon.microsoftazuread-sso.com 및 https://aadg.windows.net.nsatc.net 모두가 인트라넷 영역 내에 정의되어 있어야 합니다.  
2.  워크스테이션이 도메인에 가입되어 있는지 확인합니다.
3.  사용자가 도메인 계정으로 로그인되어 있는지 확인합니다.
4.  컴퓨터가 회사 네트워크에 연결되어 있는지 확인합니다.
5.  컴퓨터의 시간이 Active Directory와 동기화되어 있는지 도메인 컨트롤러 시간이 정확한 시간에서 5분 이내인지 확인합니다.

위의 요구 사항을 확인할 수 있는 경우에는 브라우저의 콘솔 로그에서 추가 정보를 검토합니다.  콘솔 로그는 개발자 도구 아래에서 찾을 수 있습니다.  이러한 정보는 잠재적인 문제를 확인하는 데 도움이 됩니다.

## <a name="event-log-entries"></a>이벤트 로그 항목
성공 감사가 설정되어 있으면 사용자가 Single Sign-On을 통해 로그인할 때마다 도메인 컨트롤러의 이벤트 로그에 항목이 기록됩니다.  이러한 이벤트를 찾으려면 AzureADSSOAcc$라는 컴퓨터 계정과 연결된 보안 이벤트 4769에 대한 이벤트 로그를 검토합니다.  아래 필터는 컴퓨터 계정과 연결된 모든 보안 이벤트를 찾아줍니다.

    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>






<!--HONumber=Dec16_HO3-->


