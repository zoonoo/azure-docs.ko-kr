<properties
	pageTitle="PowerApps 엔터프라이즈에 Office 365 사용자 API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Office 365 사용자 API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
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

# PowerApps 엔터프라이즈에 새 Office 365 사용자 API 만들기

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-office365-users.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-office365-users.md)

조직의 (테넌트) 앱 서비스 환경에 Office 365 사용자 API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다.  
![][14]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다.  
![][15]

4. **PowerApps**에서 **API 관리**를 선택합니다.

	![등록된 API 찾아보기][1]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. 

	![API 추가][2]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Office 365 사용자**를 선택합니다. 

![Office 365 사용자 API 선택][3]
8. **설정 - 필수 설정 구성**을 선택합니다.
 ![Office 365 사용자 API 설정 구성][4]

9. Office 365 AAD(Azure Active Directory) 응용 프로그램의 *클라이언트 ID* 및 *클라이언트 암호*를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 AAD 앱 등록" 섹션을 참조하여 필요한 ID와 암호 값을 만듭니다.

	> [AZURE.IMPORTANT] **리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.

10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Office 365 사용자 API가 앱 서비스 환경에 추가됩니다.

## 옵션: PowerApps Office 365 사용자 API에서 사용할 AAD 앱 등록

키와 암호 값을 가진 기존 AAD 앱이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들고 필요한 값을 가져옵니다.

1. [Azure 포털][5]을 엽니다.

2. **찾아보기**를 선택한 다음 **Active Directory**를 선택합니다.

	> [AZURE.NOTE] 그러면 Azure 클래식 포털에서 Active Directory가 열립니다.

3. 조직의 테넌트 이름을 선택합니다.

	![Azure Active Directory 시작][6]

4. **응용 프로그램** 탭을 선택한 다음 **추가**를 선택합니다.

	![AAD 테넌트 응용 프로그램][7]

5. **응용 프로그램 추가**에서:

	1. 응용 프로그램의 **이름**을 입력합니다.  
	2. 응용 프로그램 형식은 그대로 **웹**으로 유지합니다.  
	3. **다음**을 선택합니다.  

	![AAD 응용 프로그램 추가 - 앱 정보][8]

6. **앱 속성**에서:

	1. 응용 프로그램의 **로그온 URL**을 입력합니다. PowerApps에 대해 AAD로 인증하기 때문에 로그온 URL을 \__https://login.windows.net_으로 설정합니다.
2. 앱에 대해 올바른 **앱 ID URI**를 입력합니다.  
	3. **확인**을 선택합니다.  

	![AAD 응용 프로그램 추가 - 앱 속성][9]

7. 성공적으로 완료되면 새 AAD 앱으로 리디렉션됩니다. **구성**을 선택합니다.

	![Contoso AAD 앱][10]

8. _OAuth 2_ 섹션의 **회신 URL**을 이 항목의 Azure 포털에서 새 Office 365 사용자 API를 추가했을 때 받은 리디렉션 URL로 설정합니다. **응용 프로그램 추가**를 선택합니다.

	![Contoso AAD 앱 구성][11]

9. **다른 응용 프로그램에 대한 권한** 창에서 **Office 365 통합 API(미리 보기)**를 선택하고 **확인**을 선택합니다.

10. 구성 페이지로 돌아가 보면 _Office 365 통합 API(미리 보기)_가 _다른 응용 프로그램에 대한 권한_ 목록에 추가되었습니다.

11. _Office 365 통합 API(미리 보기)_에 대해 **위임된 권한**을 선택하고 **Read all users' basic profiles**(모든 사용자의 기본 프로필 읽기) 권한을 선택합니다.

새 Azure Active Directory 앱이 만들어졌습니다. 이 앱을 Azure 포털의 Office 365 사용자 API 구성에서 사용할 수 있습니다.

[응용 프로그램을 Azure AD에 추가하는 방법 및 이유](../active-directory/active-directory-how-applications-are-added.md)에서 AAD 응용 프로그램에 대한 몇 가지 좋은 정보를 참조하세요.

## REST API 참조

[Office 365 사용자 REST API](../connectors/create-api-office365-users.md) 참조.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Office 365 사용자 API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0309_2016-->