<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작
===========================================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

이 토픽은 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 2012 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다. 오른쪽에 있는 클립을 클릭하여 이 자습서의 동영상 버전을 볼 수 있습니다.

[자습서 보기](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) [동영상 재생](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) 15:33

**참고**

이 자습서에서는 Visual Studio 2012를 통해 만든 Windows 스토어 앱에 모바일 서비스 기능을 추가합니다. Visual Studio 2013에는 Windows 스토어 앱을 모바일 서비스에 쉽게 연결할 수 있게 해주는 새로운 기능이 포함되어 있습니다. 자세한 내용은 [모바일 서비스의 데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)을 참조하십시오.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Windows 스토어 앱 프로젝트 다운로드](#download-app)
2.  [모바일 서비스 만들기](#create-service)
3.  [저장소로 사용할 데이터 테이블 추가](#add-table)
4.  [모바일 서비스를 사용하도록 앱 업데이트](#update-app)
5.  [모바일 서비스에 대해 앱 테스트](#test-app)	

**참고**

이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28)을 참조하십시오.

프로젝트 다운로드GetStartedWithData 프로젝트 다운로드
-----------------------------------------------------

이 자습서는 Windows 스토어 앱인 [GetStartedWithData 앱](http://go.microsoft.com/fwlink/?LinkId=262308)을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1.  [개발자 코드 샘플 사이트](http://go.microsoft.com/fwlink/?LinkId=262308)(영문)에서 GetStartedWithData 샘플 앱의 C\# 버전을 다운로드합니다.

   	![][10]

2.  Visual Studio 2012 Express for Windows 8에서 다운로드한 프로젝트를 열고 MainPage.xaml.cs 파일을 검사합니다.

   	추가된 **TodoItem** 개체가 메모리 내 **ObservableCollection<TodoItem>**에 저장됩니다.

3.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

   	![][0]  

   	저장된 텍스트가 **Query and update data** 아래 두 번째 열에 표시됩니다.

모바일 서비스 만들기관리 포털에서 새 모바일 서비스 만들기
---------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

새 테이블 추가모바일 서비스에 새 테이블 추가
--------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

앱 업데이트데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트
-----------------------------------------------------------------

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2.  왼쪽 창에서 **온라인** 범주를 선택한 후 **시험판 포함**을 선택합니다. `WindowsAzure.MobileServices`를 검색하고 **Azure 모바일 서비스** 패키지에서 **설치**를 클릭한 후 사용권 계약에 동의합니다.

  	![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png) 

  	모바일 서비스 클라이언트 라이브러리가 프로젝트에 추가됩니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

   	![][8]

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

1.  Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가하거나 주석 처리를 제거합니다.

         using Microsoft.WindowsAzure.MobileServices;

2.  이 파일에서 **MobileService** 변수를 정의하는 코드의 주석 처리를 제거하고 **MobileServiceClient** 생성자에 모바일 서비스의 URL 및 응용 프로그램 키를 이 순서대로 제공합니다.

         //public static MobileServiceClient MobileService = new MobileServiceClient( 
         //    "AppUrl", 
         //    "AppKey" 
         //); 

  	모바일 서비스에 액세스하는 데 사용되는 새 **MobileServiceClient** 인스턴스가 만들어집니다.

1.  MainPage.xaml.cs 파일에서 다음 `using` 문을 추가하거나 주석 처리를 제거합니다.

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  이 파일에서 **TodoItem** 클래스 정의를 다음 코드로 바꿉니다.

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
         }

3.  기존 **항목** 컬렉션을 정의하는 줄을 주석으로 처리한 후 다음 줄의 주석 처리를 제거합니다.

         private MobileServiceCollection<TodoItem, TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.MobileService.GetTable<TodoItem>();

 이 코드를 사용하면 모바일 서비스 인식 바인딩 컬렉션(**items**) 및 SQL Database 테이블 **TodoItem**(**todoTable**)에 대한 프록시 클래스가 만들어집니다. 

4.  **InsertTodoItem** 메서드에서 **TodoItem**.**Id** 속성을 설정하는 코드 줄을 제거하고 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

         await todoTable.InsertAsync(todoItem);

  	이 코드는 테이블에 새 항목을 삽입합니다.

1.  **RefreshTodoItems** 메서드에서 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

         items = await todoTable.ToCollectionAsync();

  	이는 모바일 서비스에서 반환된 TodoItem 개체가 모두 포함된 todoTable에 있는 항목 컬렉션에 바인딩을 설정합니다. 

2.  **UpdateCheckedTodoItem** 메서드에서 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

         await todoTable.UpdateAsync(item);

  	항목 업데이트가 모바일 서비스로 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

앱 테스트모바일 서비스에 대해 앱 테스트
---------------------------------------

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  이전처럼 **Insert a TodoItem**에 텍스트를 입력하고 **Save**를 클릭합니다.

   	새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3.  [관리 포털](https://manage.windowsazure.com/)에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4.  **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]
          
   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

5.  앱에서 목록의 항목 중 하나를 선택하고 포털의 찾아보기 탭으로 돌아간 후 **새로 고침**을 클릭합니다.

   	완료 값이 **false**에서 **true**로 변경되었습니다.

1.  MainPage.xaml.cs 프로젝트 파일에서 기존 **RefreshTodoItems** 메서드를 완료된 항목을 필터링하는 다음 코드로 바꿉니다.

         private async void RefreshTodoItems()
         {                       
             // This query filters out completed TodoItems. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

2.  앱에서 목록의 항목 중 다른 하나를 선택한 후 **새로 고침** 단추를 클릭합니다.

   	이제 선택된 항목이 목록에서 사라집니다. 새로 고침할 때마다 모바일 서비스로 왕복되며, 모바일 서비스는 필터링된 데이터를 반환합니다.

이제 **데이터 시작** 자습서를 마쳤습니다.

다음 단계
---------

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보십시오.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)<br/>
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)<br/>
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보십시오.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)<br/>
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)<br/>
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)<br/>
    .NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png




[7]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
