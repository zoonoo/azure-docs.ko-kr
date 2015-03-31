<properties 
	pageTitle="푸시 시작(Android) | 모바일 개발자 센터" 
	description="Azure 모바일 서비스를 사용하여 Android .Net 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services, notification-hubs" 
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
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 퀵 스타트 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다. 




이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [데이터 시작]을 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 따라서 이 자습서를 사용하려면 Visual Studio 2013이 있어야 합니다. 

>[AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요. 

<!-- -->

>[AZURE.NOTE] 이 자습서의 Eclipse 버전을 참조하려면 [푸시 알림 시작(Eclipse)]으로 이동하세요.
 
## <a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


## <a id="configure"></a>푸시 요청을 보내도록 모바일 서비스 구성

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

> [AZURE.IMPORTANT] 포털의 푸시 탭에서 향상된 푸시 알림에 대한 GCM 자격 증명을 설정할 경우 앱에서 알림 허브를 구성하도록 GCM 자격 증명이 알림 허브와 공유됩니다.


이제 모바일 서비스가 GCM 및 알림 허브와 함께 작동하도록 구성되었습니다.


<h2><a name="download-the-service"></a>로컬 컴퓨터에 서비스 다운로드</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>모바일 서비스 테스트</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a id="update-server"></a>푸시 알림을 전송하도록 서버 업데이트

1. Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.


		using System;
		using System.Collections.Generic;

2.  `PostTodoItem` 메서드 정의를 다음 코드로 업데이트합니다.  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    이 코드는 할 일 항목을 삽입한 후 삽입한 항목의 텍스트가 포함된 푸시 알림을 보냅니다. 오류 이벤트에서 코드는 관리 포털에서 모바일 서비스의 **로그** 탭에서 볼 수 있는 오류 로그 항목을 추가합니다.


<h2><a name="publish-the-service"></a>Azure에 모바일 서비스 게시</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


## <a name="update-app"></a>앱에 푸시 알림 추가

### Android SDK 버전 확인

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다. 

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정]을 참조하세요.

### 프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### 코드 추가

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>게시된 모바일 서비스에 대해 앱 테스트</h2>

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

### 테스트에 에뮬레이터를 사용하는 경우...

Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1. **창**에서 **Android Virtual Device Manager**를 선택하고, 장치를 선택하고, **편집**(장치가 없는 경우에는 **새로 만들기**)를 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. **대상**에서 **Google API** 또는 **Google API x86**을 선택하고 확인을 클릭합니다.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	AVD 대상이 Google API를 사용하도록 설정됩니다. 몇 가지 버전의 Android SDK가 설치되어 있는 경우 API 수준이 앞서 프로젝트 속성에서 설정한 것과 일치하는지 확인합니다.

### <a id="local-testing"></a> 로컬 테스트에 푸시 알림 사용

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

### 테스트 실행

1. Eclipse의 **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작합니다.

2. 앱에서 _새 모바일 서비스 작업_과 같은 의미 있는 텍스트를 입력하고 **추가** 단추를 클릭합니다.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. 알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.


이 자습서를 성공적으로 완료했습니다.


## <a name="next-steps"> </a>다음 단계

<!---이 자습서에서는 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보낼 수 있도록 Android 앱을 설정하는 작업에 대한 기본 사항을 설명했습니다. 그다음에, 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.


+ [인증된 사용자에게 푸시 알림 보내기]
	<br/>태그를 사용하여 모바일 서비스의 푸시 알림을 인증된 사용자에게만 보내는 방법에 대해 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기]
	<br/>사용자가 관심 있어 하는 범주에 대해 푸시 알림을 등록하고 수신하는 방법을 설명합니다.

+ [구독자에게 템플릿 기반 알림 보내기]
	<br/>백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아봅니다.
-->
다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작]
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>알림 허브 솔루션 문제를 해결하고 솔루션을 디버그하기 위한 지침을 얻습니다. 

* [모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법]
  <br/>모바일 서비스를 Android와 함께 사용하는 방법을 자세히 알아보세요.  
  
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
[푸시 알림 시작(Eclipse)]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push-EC
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[페이징을 사용하여 쿼리 구체화]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[모바일 서비스 시작]: /documentation/articles/mobile-services-dotnet-backend-android-get-started
[데이터 시작]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[인증 시작]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript 및 HTML]: /develop/mobile/tutorials/get-started-with-data-js
[JavaScript 백 엔드 버전]: /develop/mobile/tutorials/get-started-with-data-android
[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[개발자 코드 샘플 사이트(영문)]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[모바일 서비스 .NET 방법 개념 참조]: /develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient 클래스(영문)]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법]: /documentation/articles/mobile-services-android-how-to-use-client-library

[인증된 사용자에게 푸시 알림 보내기]: /documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[알림 허브 정의]: /documentation/articles/notification-hubs-overview/
[구독자에게 브로드캐스트 알림 보내기]: /documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[구독자에게 템플릿 기반 알림 보내기]: /documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=47-->
