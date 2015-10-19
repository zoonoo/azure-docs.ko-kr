<properties
	pageTitle="앱 프록시를 사용하여 하이브리드 액세스 사용| Microsoft Azure"
	description="Azure Active Directory를 통해 네트워크 외부에서 개인 네트워크 내에 실행 중인 앱에 엑세스하도록 설정 합니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#앱 프록시를 사용하여 하이브리드 액세스 사용
Microsoft Azure AD 응용 프로그램 프록시로 개인 네트워크 내부에서 실행 중인 응용 프로그램을 게시하므로 네트워크 외부의 장치에 안전하게 액세스할 수 있습니다. Azure AD 앱 프록시는 개인 온-프레미스 또는 클라우드 네트워크 내부에서 응용 프로그램을 게시하는 데 사용될 수 있습니다. Azure AD 내에서 즉각적인 서비스로 구성할 수 있고 환경 내에서 서버에 응용 프로그램 프록시 커넥터를 설치할 필요가 있습니다.

##작동 방법

다음 단계는 하이브리드 환경에서 응용 프로그램이 게시되는 방법을 나열합니다.
 
1.	커넥터는 회사 네트워크에 배포됩니다. (다중 커넥터는 중복성 및 규모 확장에 배포될 수 있습니다.)
2.	커넥터는 클라우드 서비스에 자동으로 연결됩니다.
3.	사용자는 커넥터를 통해 리소스에 해당 트래픽을 라우팅하는 클라우드 서비스에 연결합니다.

다음 다이어그램은 응용 프로그램 프록시가 어떻게 작동하는지 자세한 세부 정보를 제공합니다.

 ![Azure AD 앱 프록시 작동 방식](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	사용자는 응용 프로그램 프록시를 통해 응용 프로그램에 액세스하고 인증을 위해 Azure AD 로그인 페이지로 전달됩니다.
2.	성공적인 로그인 후에 토큰을 생성하고 사용자에게 보냅니다.
3.	이제 사용자는 토큰에서 사용자 주체 이름(UPN) 및 보안 주체 이름(SPN)을 검색하고 커넥터에 요청을 전달하는 응용 프로그램 프록시에 토큰을 보냅니다.
4.	커넥터는 사용자를 가장하여 내부(Windows) 인증에 사용할 수 있는 Kerberos 티켓을 요청합니다. (Kerberos 제한 위임)
5.	 Kerberos 티켓은 Active Directory에서 검색됩니다.
6.	검색된 티켓은 이를 확인하는 응용 프로그램 서버에 전송됩니다.
7.	 응답은 응용 프로그램 프록시를 통해 최종 사용자에게 전송됩니다. 웹 응용 프로그램을 게시하기 위한 요구 사항이 하나 있습니다. 개인 네트워크의 웹 응용 프로그램은 커넥터가 설치되는 서버에서 액세스할 수 있어야 합니다. 즉, 응용 프로그램 서버 자체 또는 환경 내의 다른 서버에 커넥터를 설치할 수 있습니다. 커넥터가 웹 응용 프로그램에 액세스할 수 있으면 됩니다.

##Azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 통합하는 방법
Azure AD의 액세스 패널을 통해 Azure AD에 온-프레미스 응용 프로그램을 게시할 수 있습니다. 다음 단계를 수행해야 합니다.

1. Azure AD의 응용 프로그램 프록시를 사용하고 커넥터를 설치 및 등록합니다. 자세한 지침은 [Azure AD 응용 프로그램 프록시 사용](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md)을 참조하세요.
2. Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시- 자세한 지침은 [Azure AD 앱 프록시를 통해 응용 프로그램 게시](active-directory-application-proxy-publish.md)를 참조하세요.

<!---HONumber=Oct15_HO2-->