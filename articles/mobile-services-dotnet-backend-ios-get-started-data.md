<properties 
	pageTitle="데이터 시작(iOS) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 iOS 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하고, 새 모바일 서비스를 만들고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 변경된 데이터를 확인합니다.

이 자습서에서 만드는 모바일 서비스는 모바일 서비스의 .NET 런타임을 지원합니다. 따라서 사용자는 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전]을 참조하세요.

>[AZURE.NOTE]이 자습서는 iOS 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 여러 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [iOS 앱 프로젝트 다운로드]
2. [모바일 서비스 만들기]
3. [로컬로 서비스 다운로드]
4. [모바일 서비스 테스트]
5. [Azure에 모바일 서비스 게시]
7. [모바일 서비스를 사용하도록 앱 업데이트]
8. [모바일 서비스에 대해 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

+ [모바일 서비스 iOS SDK]와 [XCode 4.5][Xcode 설치] 및 iOS 5.0 이상 버전을 설치합니다.
+ Visual Studio 2013([Visual Studio Express for Web](http://go.microsoft.com/p/?linkid=9832232) 무료 이용 가능).
+ Microsoft Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 iOS 앱인 [GetStartedWithData 앱][GitHub]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 iOS 퀵 스타트에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1. GetStartedWithData [샘플 앱][GitHub]을 다운로드합니다.

2. 다운로드한 프로젝트를 Xcode에서 열고 TodoService.m 파일을 검사합니다.

   	이 앱이 모바일 서비스에서 작동하도록 만들려면 수행해야 하는 단계를 지정하는 8개의 **// TODO** 주석이 있습니다.

3. **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4. 앱에서 텍스트 상자에 일부 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   	![][0]  

   	저장된 텍스트가 아래 목록에 표시됩니다.

##<a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>로컬 컴퓨터에 서비스 다운로드

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>모바일 서비스 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Azure에 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트

1. Xcode에서 배포 대상 에뮬레이터(iPhone 또는 iPad)를 선택하고 **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

   그러면 모바일 서비스에서 항목을 쿼리하며 iOS SDK로 빌드된 Azure 모바일 서비스 클라이언트가 실행됩니다.

2. 앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다. Azure 관리 포털에서 모바일 서비스에 대해 이전에 구성한 SQL 데이터베이스에서 새 todoItem이 각각 저장되고 업데이트됩니다.

3. 변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 중지하고 다시 시작합니다.

	Azure 관리 포털 또는 Visual Studio의 SQL Server 개체 탐색기를 사용하여 데이터베이스를 검토할 수도 있습니다. 다음의 두 단계에서는 [Azure 관리 포털]을 사용하여 데이터베이스의 변경 사항을 확인합니다.

4. Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.

    ![][17]

5. 관리 포털에서 앱에 의해 변경된 내용을 표시하는 쿼리를 실행합니다. 쿼리는 다음과 유사하지만,  `todolist` 대신 데이터베이스 이름을 사용하게 됩니다.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

이제 **데이터 시작 자습서**를 마쳤습니다.

##<a name="next-steps"></a>다음 단계

이 자습서에서는 iOS 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 계열을 완료한 후에는 다른 iOS 자습서 중 하나를 시도해 보세요.

* [인증 시작 (영문)]
	<br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작]
  <br/>모바일 서비스를 통해 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->
[iOS 앱 프로젝트 다운로드]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps
[로컬로 서비스 다운로드]: #download-the-service-locally
[모바일 서비스 테스트]: #test-the-service
[Azure에 모바일 서비스 게시]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-ios
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-ios
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-ios
[인증 시작 (영문)]: /ko-kr/develop/mobile/tutorials/get-started-with-users-ios
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-ios
[JavaScript 백 엔드 버전]: /ko-kr/develop/mobile/tutorials/get-started-with-data-ios

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Xcode 설치]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub 리포지토리]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
