<properties
	pageTitle="PowerApps 엔터프라이즈에 SharePoint Server API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 SharePoint Server API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps 엔터프라이즈에 새 SharePoint Server API 만들기

조직의 (테넌트) 앱 서비스 환경에 SharePoint Server API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][14]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][15]

4. **PowerApps**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][5]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][6]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **SharePoint Server**를 선택합니다. 
8. **설정 - 필수 설정 구성**을 선택합니다.
9. SharePoint Server AAD(Azure Active Directory 앱)의 *클라이언트 ID*와 *앱 키*를 입력하고 AAD 프록시 앱의 *SharePoint URL* 및 *리소스 ID*를 입력합니다. 다음 섹션에서 설명하는 단계를 따라 온-프레미스 SharePoint Server에 대한 연결을 구성합니다.  

	> [AZURE.IMPORTANT] **리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.
	
10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 SharePoint Server API가 앱 서비스 환경에 추가됩니다.


## 온-프레미스 SharePoint Server에 대한 연결 구성

SharePoint Server는 사용자 인증을 위해 Active Directory를 사용합니다. 앱 서비스 환경의 API는 AAD(Azure Active Directory)를 사용하여 인증됩니다. 사용자의 AAD 토큰을 교환하고 AD 토큰으로 변환해야 합니다. 그런 다음 이 AD 토큰을 사용하여 온-프레미스 서비스에 연결할 수 있습니다.

이 요구 사항에 대해 [AAD 프록시(Azure 응용 프로그램 프록시)](../active-directory/active-directory-application-proxy-publish.md)가 사용됩니다. 이는 GA의 Azure 서비스이며 온-프레미스 웹 응용 프로그램에 대한 원격 액세스 및 SSO를 보호합니다. AAD 프록시를 사용하는 단계는 MSDN에 잘 설명되어 있습니다. 높은 수준의 단계는 다음과 같습니다.

1. [응용 프로그램 프록시 서비스 사용](../active-directory/active-directory-application-proxy-enable.md) – 다음을 포함합니다.  

	- Azure AD에서 응용 프로그램 프록시 사용
	- Azure 응용 프로그램 프록시 커넥터 설치 및 등록

2. [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](../active-directory/active-directory-application-proxy-publish.md) - 다음을 포함합니다.

	- 마법사를 사용하여 응용 프로그램 프록시 앱 게시 프록시 앱을 만든 후 인트라넷 Sharepoint 사이트의 외부 URL을 적어 둡니다.
	- 응용 프로그램에 사용자 및 그룹을 할당합니다.
	- 응용 프로그램 프록시 커넥터에서 사용하는 SPN(서비스 주체 이름)과 같은 고급 구성을 입력하여 Kerberos 토큰 온-프레미스를 가져옵니다.

프록시 앱이 만들어지면 프록시 응용 프로그램에 위임하는 다른 AAD 앱을 만들어야 합니다. 이는 동의 흐름에 필요한 액세스 토큰 및 새로 고침 토큰을 가져오는 데 필요합니다. [이러한 지침](../active-directory/active-directory-integrating-applications.md)을 수행하면 새 AAD 응용 프로그램을 만들 수 있습니다.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Office 365 Outlook API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->