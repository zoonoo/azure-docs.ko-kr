
<properties 
    pageTitle="Azure AD + Azure RemoteApp에 대한 Active Directory 요구 사항 | Microsoft Azure" 
    description="Azure RemoteApp과 작동하도록 Active Directory를 설정하는 방법에 대해 알아봅니다." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/28/2016" 
    ms.author="elizapo" />



# Azure AD + Azure RemoteApp에 대한 Active Directory 요구 사항



Azure RemoteApp 하이브리드 컬렉션 또는 AD Connect를 사용하여 페더레이션하려는 클라우드 컬렉션의 경우 다음을 수행해야 합니다.

### Azure AD 및 Active Directory 연결

Azure AD 테넌트와 온-프레미스 Active Directory 환경을 연결하려면 AD Connect를 사용합니다. [4번 클릭](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)만으로 두 개의 디렉터리를 연결할 수 있습니다.

참고 - 하이브리드 컬렉션에는 디렉터리 동기화가 필요합니다.

### "@domain.com" 일치를 확인합니다.
시작하기 전에 온-프레미스 포리스트에 대한 UPN이 Azure AD 도메인의 접미사와 일치하는지 확인합니다.

Azure AD에서 UPN 도메인 접미사를 설정한 후 Azure RemoteApp에 로그인하는 모든 사용자는 “user@<the suffix you set up>”로 로그인하게 됩니다. 또한 사용자가 동일한 user@suffix를 사용하여 온-프레미스 도메인에 로그인할 수 있는지 확인합니다. 특정한 경우에는 온-프레미스 사용자를 위해 다른 도메인 접미사를 지정하는 동안 Azure AD에 하나의 도메인 이름을 설정할 수 있습니다. 이런 경우 사용자는 Azure RemoteApp을 통해 도메인에 가입된 컴퓨터나 리소스에 연결할 수 없습니다.

예를 들어 AAD에서 UPN 도메인 접미사를 contoso.com으로 설정하고 일부 온-프레미스/AD 사용자를 @contoso.uk로 로그인하도록 구성한다면 해당 사용자는 ARA 컬렉션에 제대로 로그인할 수 없습니다. 로그인을 가능하게 하려면 AAD와 AD의 사용자 UPN이 동일해야 합니다.

### Azure RemoteApp에 대한 개체 만들기
다음과 같은 온-프레미스 Active Directory 개체도 만들어야 합니다.

- RDSH 끝점을 온-프레미스 도메인에 연결하여 RemoteApp 프로그램에 도메인 리소스에 대한 액세스 권한을 제공할 서비스 계정.
- RemoteApp 컴퓨터 개체를 포함할 OU(조직 구성 단위). 필수는 아니지만 OU를 통해 RemoteApp에 사용할 계정과 정책을 격리시키는 것이 좋습니다.

RemoteApp 컬렉션을 만들 때 이러한 두 개체가 필요하므로 이 단계를 먼저 수행해야 합니다.

<!---HONumber=AcomDC_0330_2016-->