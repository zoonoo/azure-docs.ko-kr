<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 데이터 시작
=============================

[Windows 스토어 C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")
[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript 백 엔드")

이 토픽은 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 2013 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

**참고**

이 자습서에는 Windows 스토어 앱을 모바일 서비스에 쉽게 연결할 수 있게 해 주는 Visual Studio 2013이 필요합니다. Visual Studio 2012를 사용하여 동일한 기본 절차를 완료하려면 [Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/) 토픽의 단계를 따르십시오.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Windows 스토어 앱 프로젝트 다운로드](#download-app)
2.  [모바일 서비스 만들기](#create-service)
3.  [저장소로 사용할 데이터 테이블 추가](#add-table)
4.  [모바일 서비스를 사용하도록 앱 업데이트](#update-app)
5.  [모바일 서비스에 대해 앱 테스트](#test-app)

**참고**

이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F)을 참조하십시오.

프로젝트 다운로드GetStartedWithData 프로젝트 다운로드
-----------------------------------------------------

이 자습서는 Visual Studio 2013의 Windows 스토어 앱 프로젝트인 [GetStartedWithMobileServices 앱](http://go.microsoft.com/fwlink/p/?LinkId=328660)(영문)을 기반으로 합니다. 이 앱의 UI는 추가된 항목이 로컬 메모리에 저장된다는 점을 제외하고 모바일 서비스 빠른 시작에서 생성된 앱과 동일합니다.

1.  [개발자 코드 샘플 사이트](http://go.microsoft.com/fwlink/p/?LinkId=328660)(영문)에서 GetStartedWithData 샘플 앱의 JavaScript 버전을 다운로드합니다.

   	![][10]

2.  Visual Studio 2012 Express for Windows 8에서 다운로드한 프로젝트를 열고 **js** 폴더를 확장한 후 default.js 파일을 검사합니다.

   	추가한 **TodoItem** 개체는 메모리 내 **List** 개체에 저장됩니다.

3.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

   	![][0]  

   	저장된 텍스트가 **Query and update data** 아래 두 번째 열에 표시됩니다.

모바일 서비스 만들기Visual Studio에서 새 모바일 서비스 만들기
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  솔루션 탐색기에서 **services**, **mobile services**, **&lt;your\_service\>** 폴더를 확장하고 service.js 스크립트 파일을 연 후 새 전역 변수를 확인합니다. 다음 예제와 같이 표시됩니다.

    ``` {}
    var todolistClient = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
    ```

    이 코드는 전역 변수를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 스크립트에 대한 참조가 default.html 파일에 추가되었으므로 이 페이지에서 참조된 모든 스크립트 파일도 이 변수를 사용할 수 있습니다.

새 테이블 추가모바일 서비스에 새 테이블 추가 및 앱 업데이트
-----------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  default.js 스크립트 파일에서 기존 항목 컬렉션을 정의하는 줄을 주석으로 처리한 후 다음 코드 줄을 추가하거나 주석 처리를 제거하고 프로젝트를 모바일 서비스에 연결할 때 service.js 파일에 추가된 변수로 `<yourClient>;`를 바꿉니다.

         var todoTable = <yourClient>.getTable('TodoItem');

   	이 코드는 새 데이터베이스 테이블에 대한 프록시 개체(**todoTable**)를 만듭니다. 

2.  **InsertTodoItem** 함수를 다음 코드로 바꿉니다.

         var insertTodoItem = function (todoItem) {
             // Inserts a new row into the database. When the operation completes
             // and Mobile Services has assigned an id, the item is added to the binding list.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

    이 코드는 테이블에 새 항목을 삽입합니다.

    **참고**

    새 테이블은 Id 열만 사용하여 생성됩니다. 동적 스키마가 사용하도록 설정된 경우 모바일 서비스에서 삽입 또는 업데이트 요청의 JSON 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj193175.aspx)를 참조하십시오.

3.  **RefreshTodoItems** 함수를 다음 코드로 바꿉니다.

         var refreshTodoItems = function () {
             // This code refreshes the entries in the list by querying the table. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

   	이 코드는 모바일 서비스에서 반환된 **TodoItem** 개체가 모두 포함된 todoTable의 항목 컬렉션에 바인딩을 설정합니다. 

4.  **UpdateCheckedTodoItem** 함수를 다음 코드로 바꿉니다.

         var updateCheckedTodoItem = function (todoItem) {
             // This code takes a freshly completed TodoItem and updates the database. 
             todoTable.update(todoItem);
         };

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

1.  default.js 프로젝트 파일에서 기존 **RefreshTodoItems** 함수를 완료된 항목을 필터링하는 다음 코드로 바꿉니다.

         var refreshTodoItems = function () {                     
             // More advanced query that filters out completed items. 
             todoTable.where({ complete: false })
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });            
         };

2.  앱에서 목록의 항목 중 다른 하나를 선택한 후 **새로 고침** 단추를 클릭합니다.

   	이제 선택된 항목이 목록에서 사라집니다. 새로 고침할 때마다 모바일 서비스로 왕복되며, 모바일 서비스는 필터링된 데이터를 반환합니다.

이제 **데이터 시작** 자습서를 마쳤습니다.

다음 단계
---------

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보십시오.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)<br/>
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화](/en-us/develop/mobile/tutorials/add-paging-to-data-js)<br/>
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보십시오.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-js)<br/>
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-js)<br/>
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 HTML/JavaScript 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)<br/>
    HTML 및 JavaScript와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services]: ./mobile-services-get-started.md
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[C# and XAML]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/

