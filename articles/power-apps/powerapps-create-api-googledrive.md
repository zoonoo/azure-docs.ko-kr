<properties
	pageTitle="PowerApps 엔터프라이즈에 Google 드라이브 API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Google 드라이브 API 만들기 또는 구성"
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

# PowerApps 엔터프라이즈에 새 Google Drive API 만들기

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-googledrive.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-googledrive.md)

조직의 (테넌트) 앱 서비스 환경에 Google Drive API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][15]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][16]

4. **PowerApps**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][1]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][2]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Google 드라이브**를 선택합니다. ![Google 드라이브 API 선택][3]

8. **설정 - 필수 설정 구성**을 선택합니다. ![Google 드라이브 API 설정 구성][4]

9. Google 드라이브 응용 프로그램의 *앱 키*와 *앱 암호*를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 Google 드라이브 앱 등록" 섹션을 참조하여 필요한 키와 암호 값을 만듭니다.

	> [AZURE.IMPORTANT] **리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.

10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Google 드라이브 API가 앱 서비스 환경에 추가됩니다.


## 옵션: PowerApps에서 사용할 Google 드라이브 앱 등록

키와 암호 값을 가진 기존 Google 드라이브 앱이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들고 필요한 값을 가져옵니다.

1. [Google 개발자 콘솔][5]에 로그인합니다. ![Google 개발자 콘솔][6]

2. **빈 프로젝트 생성**을 선택합니다.

3. 응용 프로그램의 이름을 입력하고 계약조건에 동의한 다음 **만들기**를 선택합니다. ![새 Google 드라이브 프로젝트 만들기][7]

4. 새 프로젝트가 성공적으로 만들어지면 **Google API 사용**을 선택합니다. ![Google API 사용][8]

5. 개요 페이지에서 **Drive API**를 선택합니다. ![Google 드라이브 API 개요][9]

6. **API 사용 설정**을 선택합니다. ![Google 드라이브 API 사용][10]

7. 드라이브 API를 사용하도록 설정할 때 **자격 증명**, **OAuth 2.0 클라이언트 ID**를 차례로 선택합니다. ![자격 증명 추가][12]

8. **동의 화면 구성**을 선택합니다.

9. **OAuth 동의 화면** 탭에서 **제품 이름**을 입력하고 **저장**을 선택합니다. ![동의 화면 구성][13]

10. 클라이언트 ID 만들기 페이지에서:

	1. **응용 프로그램 형식**에서 **웹 응용 프로그램**을 선택합니다.  
	2.  클러스터의 이름을 입력합니다.  
	3. 리디렉션 URL을 이 항목의 Azure 포털에서 새 Google Drive API를 추가했을 때 받은 리디렉션 URL로 설정합니다.  
	4. **만들기**를 선택합니다.  

	![클라이언트 ID 만들기][14]

11. 등록한 응용 프로그램의 클라이언트 ID와 클라이언트 암호가 표시됩니다.

새 Google 드라이브 앱이 만들어졌습니다. 이 앱을 Azure 포털의 Google 드라이브 API 구성에서 사용할 수 있습니다.

## REST API 참조

[Google Drive REST API](../connectors/create-api-googledrive.md) 참조.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Google 드라이브 API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->