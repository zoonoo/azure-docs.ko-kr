<properties
	pageTitle="PowerApps 엔터프라이즈에서 Microsoft 관리 및 IT 관리 API 만들기 및 구성| Microsoft Azure"
	description="PowerApps에서 사용 가능한 API 및 이들을 Azure 포털에 등록하는 방법 알아보기"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="guayan"/>

# Microsoft 관리 API 또는 IT 관리 API 등록
**Microsoft 관리** 및 **IT 관리** API가 있습니다. PowerApps Enterprise를 사용하도록 설정하면 Microsoft 관리 API를 자동으로 사용할 수 있게 됩니다. 메모리, 연결, 트러스트 등도 자동으로 관리됩니다. 다음 단계는 Twitter 계정 및 암호와 같은 특정 사용자 설정을 입력하는 것입니다.

IT 관리 API를 사용하여 메모리, 연결, 트러스트 등을 포함하여 모든 사항을 모니터링합니다. IT 관리 API는 SQL Server 및 SharePoint Server와 같은 온-프레미스 시스템에 연결할 수 있는 API도 포함하고 있습니다.

**Microsoft 관리** 또는 **IT 관리** API를 사용하려면 Azure 포털에 해당 API를 "등록"해야 합니다. 등록한 후 앱에서 이러한 API를 사용할 수 있습니다. 사용할 수 있는 옵션은 다음과 같습니다.

- Microsoft 관리 API 또는 IT 관리 API 등록
- [앱 서비스 환경](powerapps-register-api-hosted-in-app-service.md) 내에서 호스트되는 API 등록
- [Swagger 2.0 API 정의](powerapps-register-existing-api-from-api-definition.md)를 사용하여 등록

이 문서에서는 **Microsoft 관리 API 및 IT 관리 API 등록**을 집중 설명합니다.

#### 시작하기 위한 필수 조건

- [PowerApps 엔터프라이즈](powerapps-get-started-azure-portal.md)에 등록합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.


## 사용 가능한 Microsoft 관리 API 보기
**Microsoft 관리** API는 PowerApps 엔터프라이즈와 함께 제공되며 Microsoft에서도 호스트됩니다. 많은 시나리오에서 Microsoft 관리 API는 앱에 최적입니다. 예를 들어 앱이 트윗을 보내거나 OneDrive에 파일을 업로드하거나 Excel 파일의 데이터를 표시하면 이러한 Microsoft 관리 앱이 좋은 선택입니다.

몇몇 추가 혜택은 다음을 포함합니다.

- 사용자의 고유한 인스턴스를 등록하는 데 사용할 수 있는 모든 Microsoft 관리 API를 얻습니다. 
- 네트워크, 메모리 또는 보안 구성을 포함한 리소스는 자동으로 모니터링됩니다. 예를 들어 앱에서 Excel 데이터를 표시하기 위해 더 많은 메모리가 필요한 경우 더 많은 메모리가 자동으로 추가됩니다. 
- Office 및 Twitter와 같은 API와 앱 간의 트러스트는 자동으로 만들어집니다. 


#### Microsoft 관리 API

API | 설명 | 단계 링크
--- | --- | ---
![][31] | **Dropbox**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/>항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>텍스트 번역, 언어 감지 등을 수행합니다. | [**시작**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office365 Outlook**<br/><br/>메일을 관리합니다. | [**시작**](powerapps-create-api-office365-outlook.md)
![][37] | **Office365 사용자**<br/><br/>사용자 프로필, 사용자의 관리자, 사용자의 직접 보고서 등에 액세스합니다. | [**시작**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> 항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> 트윗 보내기, 트윗 검색, 폴로워 확인 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-twitter.md)


## 사용 가능한 IT 관리 API 보기
**IT 관리** API는 사용자가 제어하고 사용자가 관리합니다. Microsoft 관리 환경에서는 실행하지 마십시오. 일부 시나리오에서 사용자만의 IT 관리 환경에 이러한 API를 사용하는 것은 앱의 요구를 충족할 수 있습니다. 예를 들어 앱에서 Twitter API를 사용 하는데 조직의 Twitter 키(Microsoft Twitter 키 대신)를 사용해야 하는 경우가 있습니다. 이 상황에서는 Twitter API를 IT 관리 API로 구성하는 가장 좋습니다. 또 다른 예로, 앱이 SQL Server API를 사용하여 온-프레미스 데이터베이스에 연결하는 경우가 있습니다. IT 관리 환경에서 가상 네트워크를 설정하거나 Express 경로를 사용하여 온-프레미스에 연결할 수 있습니다. 선택은 사용자의 몫입니다.

몇몇 추가 혜택은 다음을 포함합니다.

- 네트워크, 메모리 또는 보안 구성을 포함한 리소스는 사용자가 모니터링합니다. 예를 들어 앱에서 Excel 데이터를 표시하기 위해 더 많은 메모리가 필요한 경우 사용자가 자신의 환경에서 추가할 메모리 양을 제어합니다. 
- 트러스트를 설정하고 앱 및 API 간의 보안을 제어합니다. 예를 들어 Office365 API를 Microsoft에서 관리할 수 있는지(자동 트러스트) 아니면 사용자 자신의 환경에서 Office365 API를 사용하는지(사용자 자신의 트러스트를 만듦) 여하를 결정합니다. 
- **모든** Microsoft 관리 API는 IT 관리형일 수도 있습니다. 예를 들어 사용자 자신의 Office365 인스턴스를 만들고 이 인스턴스를 완전히 제어하려면 다음과 같이 할 수 있습니다. 그런 다음 동일한 환경에서 Office365 IT 관리 API 및 Office365 Microsoft 관리 API를 사용할 수 있습니다. 이는 실제로 사용자 앱의 요구에 따라 달라집니다.
- 온-프레미스 시스템에 연결하거나 Bing 검색 API를 사용하는 경우 보안, 인증, 라이선스 등을 제어합니다.


#### IT 관리 API
> [AZURE.NOTE]참고로 **모든** Microsoft 관리 API는 IT 관리형일 수도 있습니다. 다음 API는 IT 관리 전용이며 Microsoft 관리형일 수 없습니다.

API | 설명 | 단계 링크
--- | --- | ---
![][30] | **Bing 검색**<br/><br/>검색 결과 포함, 검색 기능 추가 등을 수행합니다. | [**시작**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>항목 가져오기, 업데이트, 삭제 등을 수행할 수 있습니다. | [**시작**](powerapps-create-api-sharepointserver.md)


#### 자신의 고유한 인스턴스를 등록하는 이유

기본 Microsoft 관리 API 사용은 편리합니다. 사실 자신의 고유한 인스턴스를 IT 관리 API로 등록하면 많은 이점이 있습니다. 높은 수준에서 다음과 같은 작업을 원하는 경우 IT 관리 API를 만드는 것이 좋습니다.

- API에 대한 완전한 관리 가능, 사용자 액세스, 다른 시스템에 연결할 때의 보안, API 호출 한계, 모니터링 및 정책 등의 고급 기능 같은 이점이 있습니다.
- 앱 서비스 환경이 가상 네트워크를 지원하므로 온-프레미스 데이터에 액세스합니다.
- 단독으로 사용할 수 없는 비즈니스 사용자에 대한 API를 설정합니다.

다음 표에서 Microsoft 관리 IT API와 IT 관리 API의 기능을 비교합니다.

| 기능 | Microsoft 관리 | IT 관리 |
| ---------- | ----------------- | ------------ |
| API 호출 제한 | Microsoft가 정의 | 본인이 직접 정의(정책을 통해) |
| SaaS에 연결할 때 자신의 고유한 키 가져오기 | 지원되지 않음 | 지원됨 |
| API 사용자 액세스 | 모든 사용자에게 사용하도록 설정 | AAD 사용자 및 그룹 수준에서 완전 하게 관리 가능 |
| API 모니터링 | 지원되지 않음 | 지원됨 |
| API 정책 | 지원되지 않음 | 지원됨 |
| 연결 사용자 액세스 | 보기 전용 | AAD 사용자 및 그룹 수준에서 완전 하게 관리 가능 |
| 연결 관리 | 보기 전용 | 완전히 관리 가능 |


## Microsoft 관리 API 또는 IT 관리 API 등록

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 선택한 다음 **API 관리**를 선택합니다. ![][17]
2. API 관리에서 **추가**를 클릭합니다. ![][18]  
3. **API 추가**에 API 속성을 입력합니다.  

	- **이름**에 API에 대한 이름을 입력합니다. 여기서 입력하는 이름은 API의 런타임 URL에 포함됩니다. 이름을 조직에서 의미 있고 고유하게 만듭니다.
	- **원본**에서 **사용 가능한 API에서**를 선택합니다. ![][19]
4. **API**를 선택하고 등록할 API를 선택합니다. ![][20]
5. 특정 API를 선택하고 구성 가능한 속성을 추가합니다.
6. **추가**를 선택하여 이 단계를 완료합니다.

> [AZURE.TIP]API를 등록하는 경우 API를 앱 서비스 환경에 등록하는 것입니다. 앱 서비스 환경에 있는 경우 동일한 앱 서비스 환경 내의 다른 앱에서 사용할 수 있습니다.


## 요약 및 다음 단계

이 항목에서는 PowerApps가 기본적으로 제공하는 사용 가능한 API 중 자신의 고유한 인스턴스를 등록 하는 방법을 살펴보았습니다. 다음은 PowerApps에 대한 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [API 구성](powerapps-configure-apis.md)
- [새 API 추가](powerapps-register-from-available-apis.md)

<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---HONumber=AcomDC_1203_2015-->