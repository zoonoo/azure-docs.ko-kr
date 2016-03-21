<properties
	pageTitle="PowerApps 엔터프라이즈에 Dynamics CRM Online API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Dynamics CRM Online API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="sameerch"/>

# PowerApps 엔터프라이즈에서 새 Dynamics CRM Online API 만들기

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-crmonline.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-crmonline.md)

조직의 (테넌트) 앱 서비스 환경에 Dynamics CRM Online API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.

2. 작업 표시줄에서 **찾아보기**를 선택합니다.  
![][1]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다.  
![][2]

4. **PowerApps**에서 **API 관리**를 선택합니다.  
![등록된 API 찾아보기][3]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다.  
![API 추가][4]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.

7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Dynamics CRM Online**을 선택합니다.  
![Dynamics CRM Online API 선택][5]

8. **설정 - 필수 설정 구성**을 선택합니다.  
![Dynamics CRM Online API 설정 구성][6]

9. Dynamics CRM Online AAD(Azure Active Directory) 응용 프로그램의 **클라이언트 ID**와 **앱 키**를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 AAD 앱 등록" 섹션을 참조하여 필요한 ID와 암호 값을 만듭니다.

	> [AZURE.IMPORTANT] **리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.

10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Dynamics CRM Online API가 앱 서비스 환경에 추가됩니다.

## PowerApps Dynamics CRM Online API에서 사용할 AAD 앱 등록

1. [Azure 포털](https://portal.azure.com)을 엽니다.

2. **찾아보기**를 선택한 다음 **Active Directory**를 선택합니다.

	> [AZURE.NOTE] 그러면 Azure 클래식 포털에서 Active Directory가 열립니다.

3. 조직의 테넌트 이름을 선택합니다.  
![Azure Active Directory 시작][7]

4. **응용 프로그램** 탭을 선택한 다음 **추가**를 선택합니다.  
![AAD 테넌트 응용 프로그램][8]

5. **응용 프로그램 추가**에서:

	1. 응용 프로그램의 **이름**을 입력합니다.  
	2. 응용 프로그램 형식은 그대로 **웹**으로 유지합니다.  
	3. **다음**을 선택합니다.

	![AAD 응용 프로그램 추가 - 앱 정보][9]

6. **앱 속성**에서:

	1. 응용 프로그램의 **로그온 URL**을 입력합니다. PowerApps에 대해 AAD로 인증하기 때문에 로그온 URL을 \__https://login.windows.net_으로 설정합니다.
2. 앱에 대해 올바른 **앱 ID URI**를 입력합니다.  
	3. **확인**을 선택합니다.  

	![AAD 응용 프로그램 추가 - 앱 속성][10]

7. 성공적으로 완료되면 새 AAD 앱으로 리디렉션됩니다. **구성**을 선택합니다.  
![Contoso AAD 앱][11]

8. _OAuth 2_ 섹션의 **회신 URL**을 이 항목의 Azure 포털에서 새 Dynamics CRM Online API를 추가했을 때 받은 리디렉션 URL로 설정합니다.  
![Contoso AAD 앱 구성][12]

9. **저장**을 선택합니다.

새 Azure Active Directory 앱이 만들어졌습니다. 이 앱을 Azure 포털의 Dynamics CRM Online API 구성에서 사용할 수 있습니다.

## REST API 참조

[Dynamics CRM Online REST API](../connectors/create-api-crmonline.md)를 참조하세요.


## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Dynamics CRM Online API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0309_2016-->