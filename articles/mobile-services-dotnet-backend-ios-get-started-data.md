<properties
	pageTitle="iOS에서 기존 앱에 모바일 서비스 추가"
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
	ms.date="03/03/2015"
	ms.author="krisragh"/>

# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

이 자습서에서는 메모리 내 데이터를 저장하는 기존 앱을 다운로드하고 Azure 모바일 서비스와 작동하도록 변경합니다.

이 자습서를 시작하려면 [퀵 스타트]를 완료해야 합니다. 퀵 스타트에서 만드는 모바일 서비스를 다시 사용하게 됩니다.


## <a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 [GetStartedWithData iOS 앱]을 기반으로 합니다. 앱은 메모리에 저장된 추가 항목을 제외하면 [퀵 스타트]와 동일합니다.

[GetStartedWithData iOS 앱]을 다운로드합니다. 다운로드한 프로젝트를 Xcode에서 열고 **TodoService.m**을 검사합니다. 이 앱을 동작하도록 하는 단계를 지정하는 8개의 **// TODO** 주석이 있습니다.

## <a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>앱 테스트

1. Xcode에서 **실행**을 클릭하여 앱을 시작합니다. 텍스트를 입력하고 **+**를 클릭하여 할 일 목록에 항목을 추가합니다.

2. Azure 데이터베이스에 변경 사항이 유지되었는지 확인합니다. Azure 관리 포털 또는 Visual Studio의 SQL Server 개체 탐색기를 사용하여 데이터베이스를 검토할 수 있습니다.

3. 포털을 사용하여 데이터베이스를 검사하려면 모바일 서비스의 대시보드 페이지에서 데이터베이스 이름을 클릭하고 '관리'를 클릭하여 데이터베이스를 관리한 다음 로그인합니다. 다음 쿼리를 실행합니다. 다만, `todolist` 대신 모바일 서비스 이름을 사용합니다.

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[퀵 스타트]: mobile-services-dotnet-backend-ios-get-started.md
[GetStartedWithData iOS 앱]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!--HONumber=54-->