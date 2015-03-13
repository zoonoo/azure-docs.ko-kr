<properties 
	pageTitle="데이터 시작(Android) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 Android 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="ricksal"/>

# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

이 항목에서는 Android 앱용 백 엔드 데이터 소스로서 Azure 모바일 서비스를 사용하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스를 만들고, 데이터를 메모리에 저장하는 앱용 Eclipse Android 프로젝트를 다운로드하고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 변경된 데이터를 확인합니다.

이 자습서에서 만드는 모바일 서비스는 모바일 서비스의 .NET 런타임을 지원합니다. 따라서 사용자는 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전]을 참조하세요.

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 Visual Studio 2013이 필요합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.


1. [새 모바일 서비스 만들기]
2. [로컬로 서비스 다운로드]
3. [모바일 서비스 테스트]
4. [Azure에 모바일 서비스 게시]
5. [GetStartedWithData 프로젝트 다운로드]
4. [데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트]
5. [게시된 모바일 서비스에 대해 앱 테스트]


> [AZURE.IMPORTANT] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F"%20target="_blank)을 참조하세요. 


<h2><a name="create-service"></a>새 모바일 서비스 만들기</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service"></a>로컬 컴퓨터에 서비스 다운로드</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>모바일 서비스 테스트</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

<h2><a name="publish-the-service"></a>Azure에 모바일 서비스 게시</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<h2><a name="download-app"></a>GetStartedWithData 프로젝트 다운로드</h2>

### 샘플 코드 가져오기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code-EC.md)]

### Android SDK 버전 확인

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version-EC.md)]


### 샘플 코드 검사 및 실행

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

<h2><a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]

<h2><a name="test-app"></a>게시된 모바일 서비스에 대해 앱 테스트</h2>


앱이 백 엔드 저장소용으로 모바일 서비스를 사용하도록 업데이트되었으므로, 이제 Android 에뮬레이터나 Android 휴대폰을 사용하여 모바일 서비스에 대해 앱을 테스트할 수 있습니다.

1. **실행** 메뉴에서 **실행**을 클릭하여 프로젝트를 시작합니다.

	그러면 Android SDK를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.

5. 이전처럼 의미 있는 텍스트를 입력한 후 **추가**를 클릭합니다.

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

    변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 다시 시작할 수 있습니다. 또한 Azure 관리 포털을 사용하여 데이터 집합을 확인할 수 있습니다.  다음 두 단계에서 이 작업을 수행하여 데이터 집합의 변경 내용을 확인합니다.


4. Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. 관리 포털에서 쿼리를 실행하여 Windows 스토어 앱에 의한 변경 사항을 확인합니다. 쿼리는 다음과 유사하지만 `todolist` 대신 데이터베이스 이름을 사용하게 됩니다.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

이제 Android용 **데이터 시작** 자습서를 마쳤습니다.



## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Android 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 

<!--다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료했으므로 다음을 시도합니다.
-->

다른 자습서도 시도해보세요.

* [인증 시작]
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.
  
<!-- Anchors. -->

[새 모바일 서비스 만들기]: #create-service
[로컬로 서비스 다운로드]: #download-the-service-locally
[모바일 서비스 테스트]: #test-the-service
[GetStartedWithData 프로젝트 다운로드]: #download-app
[데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[로컬에 호스트한 서비스에 대해 Android 앱 테스트]: #test-locally-hosted
[Azure에 모바일 서비스 게시]: #publish-mobile-service
[Azure에서 호스트되는 서비스에 대해 Android 앱 테스트]: #test-azure-hosted
[게시된 모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
[JavaScript 및 HTML]: /ko-kr/develop/mobile/tutorials/get-started-with-data-js
[JavaScript 백 엔드 버전]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[개발자 코드 샘플 사이트(영문)]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient 클래스(영문)]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library  
<!--HONumber=45--> 
