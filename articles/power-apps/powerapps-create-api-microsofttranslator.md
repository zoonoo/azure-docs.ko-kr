<properties
	pageTitle="PowerApps 엔터프라이즈에 Microsoft Translator API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Microsoft Translator API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps 엔터프라이즈에 새 Microsoft Translator API 만들기

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-microsofttranslator.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

조직의 (테넌트) 앱 서비스 환경에 Microsoft Translator API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][7]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][8]

4. **PowerApps**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][1]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][2]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Microsoft Translatorr**를 선택합니다. ![Microsoft Translator API 선택][3]

8. **설정 - 필수 설정 구성**을 선택합니다. ![Microsoft Translator API 설정 구성][4]

9. Microsoft Translator 응용 프로그램의 *클라이언트 ID* 및 *클라이언트 암호*를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 Microsoft Translator 앱 등록" 섹션을 참조하여 필요한 ID와 암호 값을 만듭니다.

9. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Microsoft Translator API가 앱 서비스 환경에 추가됩니다.


## 옵션: PowerApps에서 사용할 Microsoft Translator 앱 등록

ID와 암호 값을 가진 기존 Microsoft Translator 앱이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들고 필요한 값을 가져옵니다.


1. [Azure 데이터 마켓 개발자 페이지][5]로 이동하여 Microsoft 계정으로 로그인합니다. 

2. **등록**을 선택합니다.

3. **응용 프로그램 등록**에서:

	1. **클라이언트 ID** 값을 입력합니다.  
	2. 응용 프로그램의 **이름**을 입력합니다.  
	3. **리디렉션 URL**의 더미 값을 입력합니다. 예를 들어 **https://contosoredirecturl*를 입력합니다.
4. **설명**을 입력합니다.  
	5. **만들기**를 선택합니다.  

	![응용 프로그램 등록][6]

새 Microsoft Translator 앱이 만들어졌습니다. 이 앱을 Azure 포털의 Microsoft Translator API 구성에서 사용할 수 있습니다.

## REST API 참조

[Microsoft Translator REST API](../connectors/create-api-microsofttranslator.md) 참조.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Microsoft Translator API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_0309_2016-->