<properties
	pageTitle="PowerApps 엔터프라이즈에 Bing 검색 API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Bing 검색 API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
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

# PowerApps 엔터프라이즈에 새 Bing 검색 만들기

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-bingsearch.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-bingsearch.md)

조직의 (테넌트) 앱 서비스 환경에 Bing 검색 API를 추가합니다.

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][4]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][5]

4. **PowerApps**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][2]

2. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][3]

3. API에 대한 설명이 포함된 **이름**을 입력합니다.

4. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Bing 검색**을 선택합니다.

	1. **설정 - 필수 설정 구성**을 선택합니다.
	2. *계정 키*를 입력합니다. Bing 검색 키가 없는 경우 무료 [Bing 검색 제안][1]을 만들어 키를 가져옵니다.
	3. **확인**을 선택합니다. 

5. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Bing 검색 API가 앱 서비스 환경에 추가됩니다.

## REST API 참조

[Bing 검색 REST API](../connectors/create-api-bingsearch.md) 참조.


## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Bing 검색 API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0309_2016-->