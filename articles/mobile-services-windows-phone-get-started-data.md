<properties 
	pageTitle="데이터 시작(WP8) - Azure 모바일 서비스" 
	description="Azure 모바일 서비스 Windows Phone 8 앱에서 데이터를 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 Azure 모바일 서비스를 사용하여 Windows Phone 8 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 변경된 데이터를 확인합니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a> <span class="time">오후 12:54</span></div>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Windows Phone 8 앱 프로젝트 다운로드] 
2. [모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]

이 자습서에는 Visual Studio 2012 Express for Windows Phone 8 및 Windows 8에서 실행 중인 [Windows Phone 8 SDK]가 필요합니다. 이 자습서를 완료하여 Windows Phone 8.1 앱을 만들려면 Visual Studio 2013 업데이트 2 이상 버전을 사용해야 합니다.

>[AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 Windows Phone Silverlight 8 앱 프로젝트인 [GetStartedWithData 앱][개발자 코드 샘플 사이트]를 기반으로 합니다.  

1. [개발자 코드 샘플 사이트](영문)에서 GetStartedWithData 샘플 앱 프로젝트를 다운로드합니다. 

	>[AZURE.NOTE]Windows Phone Silverlght 8.1 앱을 만들려면 다운로드한 Windows Phone Silverlight 8 앱 프로젝트의 대상 OS를 Windows Phone 8.1로 변경하기만 하면 됩니다. Windows Phone 스토어 앱을 만들려면 GetStartedWithData 샘플 앱 프로젝트의 [Windows Phone 스토어 앱 버전](http://go.microsoft.com/fwlink/p/?LinkId=397372)(영문)을 다운로드합니다. 

2. Visual Studio에서 다운로드한 프로젝트를 열고 MainPage.xaml.cs 파일을 검사합니다.

   	추가된 **TodoItem** 개체가 메모리 내 **ObservableCollection&lt;TodoItem&gt;**에 저장됩니다.

3. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4. 앱에서 텍스트 상자에 일부 텍스트를 입력한 후 **저장** 단추를 클릭합니다.

   	![][0]  

   	저장된 텍스트가 아래 목록에 표시됩니다.

<h2><a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>모바일 서비스에 새 테이블 추가</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트</h2>

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다. 

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고  `WindowsAzure.MobileServices`를 검색한 후 **Azure 모바일 서비스** 패키지에서 **설치**를 클릭하고 사용권 계약에 동의합니다.

  	![][7]

  	모바일 서비스 클라이언트 라이브러리가 프로젝트에 추가됩니다.

3. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

4. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록한 후 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록합니다.

   	![][8]

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

5. Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가하거나 주석 처리를 제거합니다.

       	using Microsoft.WindowsAzure.MobileServices;

6. 이 파일에서 **MobileService** 변수를 정의하는 코드의 주석 처리를 제거하고 **MobileServiceClient** 생성자에 모바일 서비스의 URL 및 응용 프로그램 키를 이 순서대로 제공합니다.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	모바일 서비스에 액세스하는 데 사용되는 새 **MobileServiceClient** 인스턴스가 만들어집니다.

6. MainPage.xaml.cs 파일에서 다음 `using` 문을 추가하거나 주석 처리합니다.

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. 이 파일에서 **TodoItem** 클래스 정의를 다음 코드로 바꿉니다.

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. 기존 **items** 컬렉션을 정의하는 줄을 주석으로 처리한 후 다음 줄의 주석 처리를 제거합니다.

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	이 코드를 사용하면 모바일 서비스 인식 바인딩 컬렉션(**items**) 및 SQL Database 테이블 **TodoItem**(**todoTable**)에 대한 프록시 클래스가 만들어집니다. 

7. **InsertTodoItem** 메서드에서 **TodoItem**.**Id** 속성을 설정하는 코드 줄을 제거하고 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

        await todoTable.InsertAsync(todoItem);

  	이 코드는 테이블에 새 항목을 삽입합니다.

8. **RefreshTodoItems** 메서드에서 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

        items = await todoTable.ToCollectionAsync();

   	이는 모바일 서비스에서 반환된 TodoItem 개체가 모두 포함된 todoTable에 있는 항목 컬렉션에 바인딩을 설정합니다. 

9. **UpdateCheckedTodoItem** 메서드에서 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

         await todoTable.UpdateAsync(item);

   	항목 업데이트가 모바일 서비스로 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

<h2><a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트</h2>

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2. 앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **저장**을 클릭합니다.

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]
  
   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

이제 Windows Phone 8용 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows Phone 8 앱이 모바일 서비스의 데이터로 작업할 수 있게 하는 과정의 기본 사항을 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서를 완료하세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 계열을 완료한 후에는 다음 Windows Phone 8 자습서 중 하나를 시도해 볼 수도 있습니다.

* [인증 시작](영문)
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>모바일 서비스를 통해 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.
 
<!-- Anchors. -->
[Windows Phone 8 앱 프로젝트 다운로드]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-wp8
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-wp8
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-wp8
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-wp8
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-wp8

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[개발자 코드 샘플 사이트](영문):  http://go.microsoft.com/fwlink/p/?LinkId=271146


<!--HONumber=42-->
