---
title: "응용 프로그램 프록시를 사용하는 Single Sign-On | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시를 사용하여 Single Sign-On을 제공하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c308524e41047220fbad026edb6a87f196d89580
ms.openlocfilehash: 3f293996d2565c495f707f99a0bb75bb7c24054e

---

# <a name="single-sign-on-with-application-proxy"></a>응용 프로그램 프록시를 사용하는 Single Sign-On
Single Sign-On은 Azure AD 응용 프로그램 프록시의 핵심 요소입니다. 다음 단계를 통해 최상의 사용자 환경을 제공합니다.

1. 사용자가 클라우드에 로그인합니다.  
2. 모든 보안 유효성 검사는 클라우드에서 발생합니다(사전 인증).  
3. 요청이 온-프레미스 응용 프로그램에 보내지면 응용 프로그램 프록시 커넥터는 사용자를 가장합니다. 백 엔드 응용 프로그램은 이를 도메인에 가입된 장치에서 보내는 일반 사용자로 생각합니다.

![응용 프로그램 프록시를 통해 최종 사용자에서 회사 네트워크에 액세스하는 다이어그램](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD 응용 프로그램 프록시를 사용하면 사용자에게 SSO(Single Sign-On) 환경을 제공할 수 있습니다. 다음 지침을 사용하여 SSO를 사용하여 앱을 게시합니다.

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>응용 프로그램 프록시와 함께 KCD를 사용하는 온-프레미스 IWA 앱용 SSO
Active Directory에 응용 프로그램 프록시 커넥터 사용 권한을 부여하여 사용자를 가장하고 사용자를 대신해서 토큰을 보내고 받음으로써 IWA(Windows 통합 인증)를 사용하여 응용 프로그램에 SSO(Single Sign On)를 사용하게 할 수 있습니다.

### <a name="network-diagram"></a>네트워크 다이어그램
사용자가 IWA를 사용하는 온-프레미스 응용 프로그램에 액세스를 시도할 때 이 다이어그램이 흐름을 설명합니다.

![Microsoft AAD 인증 흐름 다이어그램](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. 사용자가 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 액세스하기 위한 URL을 입력합니다.
2. 응용 프로그램 프록시는 사전 인증을 위해 Azure AD 인증 서비스에 요청을 리디렉션합니다. 이 시점에서 Azure AD는 다단계 인증 등, 모든 적용 가능한 인증 및 권한 부여 정책을 적용합니다. 사용자가 확인되면 Azure AD에서 토큰을 만들어서 사용자에게 보냅니다.
3. 사용자는 토큰을 응용 프로그램 프록시로 전달합니다.
4. 응용 프로그램 프록시는 토큰의 유효성을 검사하고 토큰에서 UPN(사용자 사용자 이름)을 검색한 다음, 요청, UPN 및 SPN(서비스 사용자 이름)을 이중 인증된 보안 채널을 통해 커넥터에 전송합니다.
5. 그러면 커넥터에서 온-프레미스 AD와 함께 KCD(Kerberos 제한 위임) 협상을 수행하여 사용자를 가장하여 Kerberos 토큰을 응용 프로그램에 가져오게 합니다.
6. Active Directory는 응용 프로그램에 대한 Kerberos 토큰을 커넥터로 보냅니다.
7. 그러면 커넥터에서 AD에서 받은 Kerberos 토큰을 사용하여 원래 요청을 응용 프로그램 서버에 보냅니다.
8. 응용 프로그램은 응답을 커넥터로 보냅니다. 그러면 해당 응답이 응용 프로그램 프록시 서비스를 거쳐 마지막으로 사용자에게 반환됩니다.

### <a name="prerequisites"></a>필수 조건
응용 프로그램 프록시에 대 한 SSO를 시작하기 전에 사용자 환경이 다음 설정 및 구성을 갖추고 준비되었는지 확인합니다.

* SharePoint 웹앱과 같은 앱이 Windows 통합 인증을 사용하도록 설정됩니다. 자세한 내용은 [Kerberos 인증 지원 사용](https://technet.microsoft.com/library/dd759186.aspx) 또는 SharePoint의 경우 [SharePoint 2013에서 Kerberos 인증 계획](https://technet.microsoft.com/library/ee806870.aspx)(영문)을 참조하세요.
* 모든 앱에는 서비스 주체 이름을 있습니다.
* 커넥터를 실행하는 서버 및 앱을 실행하는 서버가 도메인 가입 상태이고 동일한 도메인 또는 신뢰하는 도메인의 일부입니다. 도메인 가입에 대한 자세한 내용은 [컴퓨터를 도메인에 가입](https://technet.microsoft.com/library/dd807102.aspx)을 참조하세요.
* 커넥터를 실행하는 서버는 사용자에 대한 TokenGroupsGlobalAndUniversal 읽기 권한이 있습니다. 다음은 환경을 강화하는 보안에 영향을 받았을 수 있는 기본 설정입니다. 이 설정에 대한 자세한 내용은 [KB2009157](https://support.microsoft.com/en-us/kb/2009157)에 있습니다.

### <a name="active-directory-configuration"></a>Active Directory 구성
Active Directory 구성은 응용 프로그램 프록시 커넥터와 게시된 서버가 동일한 도메인에 있는지 여부에 따라 다양합니다.

#### <a name="connector-and-published-server-in-the-same-domain"></a>동일한 도메인 내의 커넥터와 게시된 서버
1. Active Directory에서 **도구** > **사용자 및 컴퓨터**로 이동합니다.
2. 커넥터를 실행하는 서버를 선택합니다.
.3. 마우스 오른쪽 단추를 클릭하고 **속성** > **위임**을 선택합니다.
4. **지정한 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**를 선택합니다. **이 계정으로 위임된 자격 증명을 사용할 수 있는 서비스** 아래에서 해당 응용 프로그램 서버의 SPN ID 값을 추가합니다.
5. 그러면 응용 프로그램 프록시 커넥터가 목록에 정의된 응용 프로그램에 대해 AD에서 사용자를 가장할 수 있습니다.

![커넥터 SVR 속성 창 스크린샷](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>다른 도메인 내의 커넥터 및 게시된 서버
1. 도메인에 걸쳐 KCD로 작업하기 위한 필수 구성 요소 목록은 [도메인 간의 Kerberos 제한 위임](https://technet.microsoft.com/library/hh831477.aspx)을 참조하세요.
2. Windows 2012 R2에서 커넥터 서버에 있는 `principalsallowedtodelegateto` 속성을 사용하여 응용 프로그램 프록시가 커넥터 서버에 대해 위임할 수 있게 설정하십시오. 이때, 게시된 서버는 `sharepointserviceaccount`이며 위임하는 서버는 `connectormachineaccount`입니다.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount`은 해당 SPS 앱 풀이 실행되고 있는 SPS 컴퓨터 계정 또는 서비스 계정이 될 수 있습니다.
>
>

### <a name="azure-classic-portal-configuration"></a>Azure 클래식 포털 구성
1. [응용 프로그램 프록시로 응용 프로그램 게시](active-directory-application-proxy-publish.md)에 설명된 지침에 따라 응용 프로그램을 게시합니다. 반드시 **Azure Active Directory**를 **사전 인증 메서드**로 선택해야 합니다.
2. 응용 프로그램이 응용 프로그램 목록에 나타나면 선택하고 **구성**을 클릭합니다.
3. **속성**에서 **내부 인증 방법**을 **Windows 통합 인증**으로 설정합니다.  
   ![고급 응용 프로그램 구성](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. 응용 프로그램 서버의 **내부 응용 프로그램 SPN** 을 입력합니다. 이 예에서는 게시된 응용 프로그램에 대한 SPN이 http/lob.contoso.com입니다.  

> [!IMPORTANT]
> 온-프레미스 UPN과 Azure Active Directory의 UPN이 동일하지 않은 경우 사전 인증이 작동하려면 [위임된 로그인 ID](#delegated-login-identity)를 구성해야 합니다.
>
>

|  |  |
| --- | --- |
| 내부 인증 방법 |사전 인증에 Azure AD를 사용하는 경우 이 응용 프로그램에 SSO(Single Sign-On)의 장점을 활용할 수 있도록 내부 인증 방법을 설정할 수 있습니다. <br><br> 응용 프로그램이 IWA를 사용하며, 이 응용 프로그램에 SSO를 사용하도록 KCD(Kerberos 제한 위임)를 구성한 경우 **IWA(Windows 통합 인증)**를 선택합니다. IWA를 사용하는 응용 프로그램은 KCD를 사용하여 구성해야 합니다. 그렇지 않으면 응용 프로그램 프록시에서 이러한 응용 프로그램을 게시할 수 없습니다. <br><br> 응용 프로그램이 IWA를 사용하지 않는 경우 **없음**을 선택합니다. |
| 내부 응용 프로그램 SPN |온-프레미스 Azure AD에 구성된 대로 내부 응용 프로그램의 SPN(서비스 사용자 이름)입니다. SPN은 응용 프로그램 프록시 커넥터가 KCD를 사용하여 응용 프로그램에 대한 Kerberos 토큰을 가져오는 데 사용합니다. |

## <a name="sso-for-non-windows-apps"></a>비 Windows 앱에 대한 SSO
클라우드에서 Azure AD가 사용자를 인증할 때 Azure AD 응용 프로그램 프록시에서 Kerberos 위임 흐름이 시작됩니다. 요청이 온-프레미스에 도착하면 Azure AD 응용 프로그램 프록시 커넥터는 로컬 Active Directory와 상호 작용하여 사용자 대신 Kerberos 티켓을 발급합니다. 해당 과정은 Kerberos 제한 위임(KCD)이라고 합니다. 다음 단계에서 요청은 백 엔드 응용 프로그램에 Kerberos 티켓으로 전송됩니다. 이러한 요청을 보내는 방법을 정의하는 몇 가지 프로토콜이 있습니다. 현재 비 Windows Server는 대부분 Azure AD 응용 프로그램 프록시에 지원되는 Negotiate/SPNego를 참조합니다.

![비 Windows SSO 다이어그램](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

Kerberos에 대한 자세한 내용은 [KCD(Kerberos Constrained Delegation)에 대해 확인하려는 모든 정보](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)를 참조하세요.

### <a name="delegated-login-identity"></a>위임된 로그인 ID
위임된 로그인 ID를 사용하면 다른 두 개의 로그인 시나리오를 처리할 수 있습니다.

* 일반적으로 전자 메일 주소가 아닌 사용자 이름 또는 SAM 계정 이름 형식의 사용자 ID((username@domain))를 가져오는 비Windows 응용 프로그램
* Azure AD의 UPN과 온-프레미스 Active Directory의 UPN이 다른 대체 로그인 구성.

응용 프로그램 프록시를 사용하여 Kerberos 티켓을 얻기 위해 어떤 ID를 사용할지 선택할 수 있습니다. 이 설정은 응용 프로그램별입니다. 이러한 옵션 중 일부는 전자 메일 주소 형식을 받아들이지 않는 시스템에 적합한 반면 다른 것들은 대체 로그인을 위해 설계되었습니다.

![위임된 로그인 ID 매개 변수 스크린샷](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

위임된 로그인 ID가 사용되는 경우 값은 조직 내의 모든 도메인 또는 포리스트에 대해 고유하지 않을 수도 있습니다. 다른 두 가지 커넥터 그룹을 사용하여 이러한 응용 프로그램을 두 번 게시함으로써 이 문제를 방지할 수 있습니다. 각 응용 프로그램에는 다른 사용자 대상 그룹이 있으므로 다른 도메인에 해당 커넥터를 조인할 수 있습니다.

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>온-프레미스 및 클라우드 ID가 동일하지 않는 경우 SSO를 사용하여 작업
달리 구성하지 않는 한 응용 프로그램 프록시는 사용자가 클라우드 및 온-프레미스에서 정확히 동일한 ID를 사용한다고 가정합니다. 각 응용 프로그램에 Single Sign-On을 수행할 때 어떤 ID를 사용해야 하는지 구성할 수 있습니다.  

이 기능을 사용하면 다른 온-프레미스 및 클라우드 ID가 있는 여러 조직이 사용자에게 다른 사용자 이름 및 암호를 입력하도록 하지 않고 클라우드에서 온-프레미스 앱으로 SSO를 갖게 할 수 있습니다. 이 작업은 다음의 조직을 포함합니다.

* 내부적으로 여러 도메인((joe@us.contoso.com,, joe@eu.contoso.com)) 및 클라우드의 단일 도메인((joe@contoso.com))이 있습니다.
* 내부적으로 라우팅이 가능하지 않은 도메인 이름(joe@contoso.usa)과 클라우드에서 법적 도메인이 있습니다.
* 내부적으로 도메인 이름을 사용하지 마십시오(joe).
* 온-프레미스 및 클라우드에서 다른 별칭을 사용합니다. 예: joe-johns@contoso.com 및 joej@contoso.com  

또한 비Windows 백 엔드 서버의 경우 매우 일반적인 시나리오인 전자 메일 주소 형식의 주소를 허용하지 않는 응용 프로그램에는 도움이 됩니다.

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>다른 클라우드 및 온-프레미스 ID에 SSO 설정
1. 주 ID가 전자 메일 주소가 되도록 Azure AD Connect 설정을 구성합니다(mail). 이 작업은 동기화 설정에서 **사용자 계정 이름** 필드를 변경하여 사용자 지정 프로세스의 일부로 수행됩니다. 또한 이러한 설정은 사용자가 ID 저장소로 Azure AD를 사용하는 Office&365;, Windows&10; 장치 및 다른 응용 프로그램에 로그인하는 방법을 결정합니다.  
   ![사용자 식별 스크린샷 - 사용자 계정 이름 드롭다운](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 수정하려는 응용 프로그램에 대한 응용 프로그램 구성 설정에서 사용할 **위임된 로그인 ID** 를 선택합니다.

   * 사용자 계정 이름: joe@contoso.com  
   * 대체 사용자 계정 이름: joed@contoso.local  
   * 사용자 계정 이름의 사용자 이름 일부: joe  
   * 대체 사용자 계정 이름의 사용자 이름 일부: joed  
   * 온-프레미스 SAM 계정 이름: 온-프레미스 도메인 컨트롤러 구성에 따라 지정

   ![위임된 로그인 ID 드롭다운 메뉴 스크린샷](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>다른 ID에 대한 SSO 문제 해결
SSO 프로세스에 오류가 있으면 [문제 해결](active-directory-application-proxy-troubleshoot.md)에서 설명한 대로 커넥터 컴퓨터 이벤트 로그에 표시됩니다.
그러나 어떤 경우에는 백 엔드 응용 프로그램에 요청을 성공적으로 보내는 반면 해당 응용 프로그램에서 다양한 다른 HTTP 응답으로 회신합니다. 이러한 경우 문제를 해결하려면 응용 프로그램 프록시 세션 이벤트 로그에서 커넥터 컴퓨터에 있는 이벤트 번호 24029의 검사를 시작해야 합니다. 위임에 사용된 사용자 ID는 이벤트 세부 정보의 "user" 필드에 표시됩니다. 세션 로그를 켜려면 이벤트 뷰어 보기 메뉴에서 **분석 및 디버그 로그 표시** 를 선택합니다.

## <a name="see-also"></a>참고 항목
* [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
* [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)
* [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
* [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Feb17_HO2-->


