<properties 
	pageTitle="API 앱 패키지 만들기" 
	description="Azure 마켓플레이스에 API 앱 패키지를 게시하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Azure 마켓플레이스에 API 앱 패키지 게시

## 개요

이 문서에서는 API 앱 패키지를 [Azure 마켓플레이스](http://azure.microsoft.com/marketplace/api-apps/)에 게시하는 방법을 보여 줍니다.

- API 앱을 만드는 방법을 알아보려면 [Visual Studio를 사용하여 API 앱 만들기](app-service-dotnet-create-api-app.md)를 참조하세요.
- API 앱 패키지를 만드는 방법을 알아보려면 [API 앱 패키지 만들기](app-service-api-create-package)를 참조하세요.

## 일반적인 게시 흐름

다음은 일반적인 게시 흐름입니다.

1. [API 앱 패키지 만들기](app-service-api-create-package) 자습서의 지침에 따라 API 앱에 대한 Nuget 패키지를 만듭니다.
2. Nuget 지원 갤러리\(https://apiapps.nuget.org) 에 게시합니다.
3. 그러면 [Azure 마켓플레이스](http://azure.microsoft.com/marketplace/api-apps/)에 자동으로 동기화됩니다.
4. [Azure 마켓플레이스](http://azure.microsoft.com/marketplace/api-apps/) 및 [Azure Preview 포털](https://portal.azure.com)로 이동하여 종단 간 환경을 확인합니다.

## Nuget 지원 갤러리에 게시

1. https://apiapps.nuget.org로 이동합니다.

    ![Nuget 지원 갤러리 홈 페이지](./media/app-service-api-publish-package/nuget-homepage.png)

2. **로그인**을 클릭하여 Azure 계정으로 로그인합니다.
3. **API 앱** 탭을 클릭하고 **API 앱 업로드**를 클릭한 다음 API 앱 패키지를 업로드합니다.

    ![Nuget 지원 갤러리 패키지 업로드 페이지](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. **네임스페이스**의 드롭다운 목록에 Azure 계정의 Azure Active Directory 테넌트에 대한 확인된 도메인이 모두 표시됩니다. API 앱 패키지의 apiapp.json 파일에 있는 네임스페이스 속성과 일치하는 네임스페이스를 선택해야 합니다. 그래야 게시자가 해당 API 앱 패키지에 대한 유효한 네임스페이스를 청구할 수 있습니다.
5. **이 API 앱 등록 취소**의 선택이 취소되어 있는지 확인합니다.
6. **게시**를 클릭합니다.
7. 유효성 검사 오류가 있는 경우 오류를 해결하고 다시 업로드합니다.

## Azure 마켓플레이스에 API 앱 패키지 보기

몇 분 후 API 앱 패키지가 Azure 마켓플레이스에 동기화됩니다. [여기](http://azure.microsoft.com/marketplace/api-apps/)로 이동하여 제목, 설명, 아이콘 등을 확인할 수 있습니다. 변경해야 하는 사항이 있는 경우 API 앱 패키지에서 변경하고 다시 게시하면 됩니다.

![Azure 마켓플레이스 API 앱 페이지](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Azure Preview 포털에서 API 앱 패키지 배포

Azure 계정\(API 앱 패키지를 게시하는 데 사용하는 것과 다른 계정일 수 있음\)을 사용하여 [Azure Preview 포털](https://portal.azure.com)에 로그인할 수도 있습니다. 여기에서 Azure Preview 포털의 API 앱 패키지 만들기 환경을 확인할 수 있습니다. 변경해야 하는 사항이 있는 경우 API 앱 패키지에서 변경하고 다시 게시하면 됩니다.

Azure 포털에서 API 앱 패키지를 배포하는 방법에 대한 자세한 내용은 [여기](app-service-api-connnect-your-app-to-saas-connector.md)에서 DropboxConnector 배포 예제를 참조하세요.

<!--HONumber=52-->
