<properties
	pageTitle="앱 프록시를 사용하여 하이브리드 액세스 사용| Microsoft Azure"
	description="Azure Active Directory를 통해 네트워크 외부에서 개인 네트워크 내에 실행 중인 앱에 엑세스하도록 설정 합니다."
	services="active-directory"
	documentationCenter=""
	keywords="응용 프로그램 액세스, 앱 프록시, 하이브리드 액세스"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# 앱 프록시를 사용하여 하이브리드 액세스 사용
Microsoft Azure Active Directory(AD) 응용 프로그램 프록시를 사용하여 어디서든 어떤 장치에서든 안전하게 개인 네트워크 내부에 위치한 응용 프로그램에 액세스를 제공할 수 있습니다. 환경 내에서 응용 프로그램 프록시 커넥터를 설치한 후에 Azure AD를 이용하여 쉽게 구성될 수 있습니다

웹 응용 프로그램에 액세스하기 위한 요구 사항이 하나 있습니다. 웹 응용 프로그램은 커넥터가 설치된 서버에 액세스할 수 있어야 한다는 점입니다. 즉, 커넥터가 웹 응용 프로그램에 액세스할 수 있는 한 응용 프로그램 서버 자체 또는 환경 내의 다른 서버에 커넥터를 설치할 수 있습니다.

##작동 방법
### 빠른 개요
1. 커넥터는 온-프레미스 네트워크에 배포됩니다. (다중 커넥터는 중복성 및 규모 확장에 배포될 수 있습니다.)
2. 커넥터는 클라우드 서비스에 연결됩니다.
3. 커넥터 및 클라우드 서비스는 사용자 트래픽을 응용 프로그램에 라우팅합니다.

 ![Azure AD 앱 프록시 다이어그램](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### 자세히 보기
1. 사용자는 응용 프로그램 프록시를 통해 응용 프로그램에 액세스하고 인증을 위해 Azure AD 로그인 페이지로 전달됩니다.
2. 성공적인 로그인 후에 토큰을 생성하고 사용자에게 보냅니다.
3. 사용자는 토큰에서 사용자 주체 이름(UPN) 및 보안 주체 이름(SPN)을 검색하고 커넥터에 요청을 전달하는 응용 프로그램 프록시에 토큰을 보냅니다.
4. 커넥터는 사용자를 대신하여 내부(Windows) 인증에 사용할 수 있는 Kerberos 티켓을 요청합니다. Kerberos 제한 위임 정보라고 합니다.
5. Kerberos 티켓은 Active Directory에서 검색됩니다.
6. 티켓은 응용 프로그램 서버에 전송되고 확인됩니다.
7. 응답은 응용 프로그램 프록시를 통해 최종 사용자에게 전송됩니다.

## 관련된 문서
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [Azure AD 응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Azure AD 앱 프록시를 통해 응용 프로그램 게시](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0218_2016-->