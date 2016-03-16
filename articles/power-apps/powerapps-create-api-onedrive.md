<properties
	pageTitle="PowerApps 엔터프라이즈에 OneDrive API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 OneDrive API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 조직의 앱 서비스 환경에서 새 OneDrive API 만들기

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][14]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][15]

4. **PowerApps**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][1]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][2]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **OneDrive**를 선택합니다. ![OneDrive API 선택][3]

8. **설정 - 필수 설정 구성**을 선택합니다. ![OneDrive API 설정 구성][4]

9. OneDrive 응용 프로그램의 *앱 키*와 *앱 암호*를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 OneDrive 앱 등록" 섹션을 참조하여 필요한 키와 암호 값을 만듭니다.

	> [AZURE.IMPORTANT]**리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.

10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 OneDrive API가 앱 서비스 환경에 추가됩니다.

## 옵션: PowerApps에서 사용할 OneDrive 앱 등록

키와 암호 값을 가진 기존 OneDrive 앱이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들고 필요한 값을 가져옵니다.

1. _Microsoft 계정 개발자 센터_의 [앱 만들기 페이지][5]로 이동하고 _Microsoft 계정_으로 로그인합니다.

2. **응용 프로그램 이름**을 입력하고 **동의함**을 선택합니다. ![OneDrive 새 앱][6]

3. 설정 페이지에서:

	a) **API 설정**을 선택합니다. b) 리디렉션 URL을 이 항목의 Azure 포털에서 새 OneDrive API를 추가했을 때 받은 리디렉션 URL로 설정합니다. c) **저장**을 선택합니다.

	![OneDrive 앱 API 설정][7]

새 OneDrive 앱이 만들어졌습니다. 이 앱을 Azure 포털의 OneDrive API 구성에서 사용할 수 있습니다.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 OneDrive API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_1203_2015-->