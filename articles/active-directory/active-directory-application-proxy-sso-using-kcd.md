<properties
	pageTitle="응용 프로그램 프록시와 함께 KCD를 사용하는 온-프레미스 IWA 앱용 SSO"
	description="Azure AD 응용 프로그램 프록시를 작동시키는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# 응용 프로그램 프록시와 함께 KCD를 사용하는 온-프레미스 IWA 앱용 SSO


Active Directory에 응용 프로그램 프록시 커넥터 사용 권한을 부여하여 사용자를 가장하고 사용자를 대신해서 토큰을 보내고 받음으로써 IWA(Windows 통합 인증)를 사용하여 응용 프로그램에 SSO(Single Sign On)를 사용하게 할 수 있습니다.

> [AZURE.IMPORTANT]응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.


## 네트워크 다이어그램

![Microsoft AAD 인증 흐름 다이어그램](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

사용자가 IWA를 사용하는 온-프레미스 응용 프로그램에 액세스를 시도할 때 이 다이어그램이 흐름을 설명합니다. 일반적인 흐름은 다음과 같습니다.

1. 사용자가 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 액세스하기 위한 URL을 입력합니다.
2. 응용 프로그램 프록시는 사전 인증을 위해 Azure AD 인증 서비스에 요청을 리디렉션합니다. 이 시점에서 Azure AD는 다단계 인증 등, 모든 적용 가능한 인증 및 권한 부여 정책을 적용합니다. 사용자가 확인되면 Azure AD에서 토큰을 만들어서 사용자에게 보냅니다.
3. 사용자는 토큰을 응용 프로그램 프록시로 전달합니다.
4. 응용 프로그램 프록시는 토큰의 유효성을 검사하고 토큰에서 UPN(사용자 주체 이름)을 검색한 다음, 요청, UPN 및 SPN(서비스 주체 이름)을 이중 인증된 보안 채널을 통해 커넥터에 전송합니다.
5. 그러면 커넥터에서 온-프레미스 AD와 함께 KCD(Kerberos 제한 위임) 협상을 수행하여 사용자를 가장하여 Kerberos 토큰을 응용 프로그램에 가져오게 합니다.
6. Active Directory는 응용 프로그램에 대한 Kerberos 토큰을 커넥터로 보냅니다.
7. 그러면 커넥터에서 AD에서 받은 Kerberos 토큰을 사용하여 원래 요청을 응용 프로그램 서버에 보냅니다.
8. 응용 프로그램은 응답을 커넥터로 보냅니다. 그러면 응답이 응용 프로그램 프록시 서비스를 거쳐 마지막으로 사용자에게 반환됩니다.

### 필수 조건

1. SharePoint 웹앱과 같은 앱이 Windows 통합 인증을 사용하도록 설정되었는지 확인합니다. 자세한 내용은 [Kerberos 인증에 대한 지원을 사용하도록 설정](https://technet.microsoft.com/library/dd759186.aspx) 또는 SharePoint의 경우에는 [SharePoint 2013에서 Kerberos 인증에 대한 계획](https://technet.microsoft.com/library/ee806870.aspx)을 참조하십시오.
2. 응용 프로그램에 대한 서비스 주체 이름을 만듭니다.
3. 커넥터를 실행하는 서버 및 게시하고자 하는 앱을 실행하는 서버가 도메인 가입 상태이고 동일한 도메인의 일부인지 확인합니다. 도메인 가입에 대한 자세한 내용은 [컴퓨터를 도메인에 가입](https://technet.microsoft.com/library/dd807102.aspx)을 참조하십시오.


## Active Directory 구성

Active Directory 구성은 응용 프로그램 프록시 커넥터와 게시된 서버가 동일한 도메인에 있는지 여부에 따라 다양합니다.

### 동일한 도메인 내의 커넥터와 게시된 서버

Active Directory에서 **도구** > **사용자 및 컴퓨터**로 이동합니다. 커넥터를 실행하는 서버를 선택합니다. **속성** > **위임**을 마우스 오른쪽 단추로 클릭하여 선택합니다. **지정된 서비스에 대한 위임에 대해서만 이 컴퓨터 트러스트**를 선택하고 **이 계정이 위임된 자격 증명을 나타낼 수 있는 서비스** 아래에서 해당 응용 프로그램 서버의 SPN(서비스 주체 이름) ID 값을 추가합니다. 그러면 응용 프로그램 프록시 커넥터가 목록에 정의된 응용 프로그램에 대해 AD에서 사용자를 가장할 수 있습니다.

![커넥터 SVR 속성 창 스크린샷](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### 다른 도메인 내의 커넥터 및 게시된 서버

1. 도메인에 걸쳐 KCD로 작업하기 위한 필수 구성 요소 목록은 [도메인 간의 Kerberos 제한 위임](https://technet.microsoft.com/library/hh831477.aspx)을 참조하십시오.
2. Windows 2012 R2에서 커넥터 서버에 있는 `principalsallowedtodelegateto` 속성을 사용하여 응용 프로그램 프록시가 커넥터 서버에 대해 위임할 수 있게 설정하십시오. 이때, 게시된 서버는 `sharepointserviceaccount`이며 위임하는 서버는 `connectormachineaccount`입니다.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount`은 해당 SPS 앱 풀이 실행되고 있는 SPS 컴퓨터 계정 또는 서비스 계정이 될 수 있습니다.


## Azure 포털 구성

1. [응용 프로그램 프록시로 응용 프로그램 게시](active-directory-application-proxy-publish.md)에 설명된 지침에 따라 응용 프로그램을 게시합니다. 반드시 **Azure Active Directory**를 **사전 인증 메서드**로 선택해야 합니다.
2. 응용 프로그램이 응용 프로그램 목록에 나타나면 선택하고 **구성**을 클릭합니다.
3. **속성** 아래에서 **내부 인증 방법**을 **Windows 통합 인증**으로 설정합니다.

![고급 응용 프로그램 구성](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. 응용 프로그램 서버의 **내부 응용 프로그램 SPN**을 입력합니다. 이 예에서는 게시된 응용 프로그램에 대한 SPN이 http/lob.contoso.com입니다.

>[AZURE.IMPORTANT]Azure Active Directory 내의 UPN이 온-프레미스 Active Directory 내의 UPN와 동일해야 사전 인증이 작동합니다. Azure Active Directory가 온-프레미스 Active Directory와 동기화되었는지 확인합니다.

| | |
| --- | --- |
| 내부 인증 방법 | 사전 인증에 Azure AD를 사용하는 경우 이 응용 프로그램에 SSO(Single-Sign On)의 장점을 활용할 수 있도록 내부 인증 방법을 설정할 수 있습니다. <br><br> 응용 프로그램이 IWA를 사용하며, 이 응용 프로그램에 SSO를 사용하도록 KCD(Kerberos 제한 위임)를 구성한 경우 **IWA(Windows 통합 인증)**를 선택합니다. IWA를 사용하는 응용 프로그램릏 KCD를 사용하여 구성해야 합니다. 그렇지 않으면 응용 프로그램 프록시가 이러한 응용 프로그램을 게시할 수 없습니다. <br><br>응용 프로그램이 IWA를 사용하지 않는 경우 **없음**을 선택합니다. |
| 내부 응용 프로그램 SPN | 온-프레미스 Azure AD에 구성된 대로 내부 응용 프로그램의 SPN(서비스 보안 주체 이름)입니다. SPN은 응용 프로그램 프록시 커넥터가 KCD를 사용하여 응용 프로그램에 대한 Kerberos 토큰을 가져오는 데 사용합니다. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=August15_HO9-->