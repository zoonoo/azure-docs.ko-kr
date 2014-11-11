<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

이 토픽은 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

<div class="dev-callout"><b>참고</b>
<p>이 자습서에서는 Visual Studio 2012를 통해 만든 Windows 스토어 앱에 모바일 서비스 기능을 추가합니다. Visual Studio 2013에는 Windows 스토어 앱을 모바일 서비스에 쉽게 연결할 수 있게 해주는 새로운 기능이 포함되어 있습니다. 자세한 내용은 <a href="/ko-kr/develop/mobile/tutorials/get-started-with-data-js/">모바일 서비스의 데이터 시작</a>을 참조하십시오.</p>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Windows 스토어 앱 프로젝트 다운로드][Windows 스토어 앱 프로젝트 다운로드]
2.  [모바일 서비스 만들기][모바일 서비스 만들기]
3.  [저장소로 사용할 데이터 테이블 추가][저장소로 사용할 데이터 테이블 추가]
4.  [모바일 서비스를 사용하도록 앱 업데이트][모바일 서비스를 사용하도록 앱 업데이트]
5.  [모바일 서비스에 대해 앱 테스트][모바일 서비스에 대해 앱 테스트]

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="download-app"></a><span class="short-header">프로젝트 다운로드</span>GetStartedWithData 프로젝트 다운로드

이 자습서는 Windows 스토어 앱인 [GetStartedWithData 앱][GetStartedWithData 앱]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1.  [개발자 코드 샘플 사이트][GetStartedWithData 앱](영문)에서 GetStartedWithData 샘플 앱의 JavaScript 버전을 다운로드합니다.

    ![][0]

2.  Visual Studio 2012 Express for Windows 8에서 다운로드한 프로젝트를 열고 **js** 폴더를 확장한 후 default.js 파일을 검사합니다.

    추가된 **TodoItem** 개체가 메모리 내 **List** 개체에 저장됩니다.

3.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

    ![][1]

    저장된 텍스트가 **Query and update data** 아래 두 번째 열에 표시됩니다.

## <a name="create-service"></a><span class="short-header">모바일 서비스 만들기</span>관리 포털에서 새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">새 테이블 추가</span>모바일 서비스에 새 테이블 추가

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">앱 업데이트</span>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2.  왼쪽 창에서 **온라인** 범주를 선택하고 `WindowsAzure.MobileServices.WinJS`를 검색하고 **Azure Mobile Services for WinJS** 패키지에서 **설치**를 클릭한 다음 사용권 계약에 동의합니다.

    ![][2]

    모바일 서비스 클라이언트 라이브러리가 프로젝트에 추가됩니다.

3.  default.html 프로젝트 파일에서 페이지 머리글에 다음 스크립트 참조를 추가합니다.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

5.  **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

    ![][3]

    앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

6.  Visual Studio에서 default.js 파일을 열고 **client** 변수를 정의하는 다음 코드에 달린 주석을 제거하고 **MobileServiceClient** 생성자에 모바일 서비스의 URL 및 응용 프로그램 키를 이 순서대로 제공합니다.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    모바일 서비스에 액세스하는 데 사용되는 새 MobileServiceClient 인스턴스가 만들어집니다.

7.  다음 코드 줄에 달린 주석을 제거합니다.

        var todoTable = client.getTable('TodoItem');

    이 코드는 새 데이터베이스 테이블에 대한 프록시 개체(**todoTable**)를 만듭니다.

8.  **InsertTodoItem** 함수를 다음 코드로 바꿉니다.

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    이 코드는 테이블에 새 항목을 삽입합니다.

9.  **RefreshTodoItems** 함수를 다음 코드로 바꿉니다.

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    이는 모바일 서비스에서 반환된 모든 완료 항목이 포함된 todoTable에 있는 항목 컬렉션에 바인딩을 설정합니다.

10. **UpdateCheckedTodoItem** 함수를 다음 코드로 바꿉니다.

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    항목 업데이트가 모바일 서비스로 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

## <a name="test-app"></a><span class="short-header">앱 테스트</span>모바일 서비스에 대해 앱 테스트

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  이전처럼 **Insert a TodoItem**에 텍스트를 입력하고 **Save**를 클릭합니다.

    그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3.  [관리 포털][관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4.  **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

    ![][4]

    이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

5.  앱에서 목록의 항목 중 하나를 선택하고 포털의 찾아보기 탭으로 돌아간 후 **새로 고침**을 클릭합니다.

    완료 값이 **false**에서 **true**로 변경되었습니다.

6.  default.js 프로젝트 파일에서 기존 **RefreshTodoItems** 함수를 완료된 항목을 필터링하는 다음 코드로 바꿉니다.

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  앱에서 목록의 항목 중 다른 하나를 선택한 후 **새로 고침** 단추를 클릭합니다.

    이제 선택된 항목이 목록에서 사라집니다. 새로 고침할 때마다 모바일 서비스로 왕복되며, 모바일 서비스는 필터링된 데이터를 반환합니다.

이제 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정][스크립트를 사용하여 데이터 유효성 검사 및 수정]
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보십시오.

-   [인증 시작][인증 시작]
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.



  [Windows 스토어 앱 프로젝트 다운로드]: #download-app
  [모바일 서비스 만들기]: #create-service
  [저장소로 사용할 데이터 테이블 추가]: #add-table
  [모바일 서비스를 사용하도록 앱 업데이트]: #update-app
  [모바일 서비스에 대해 앱 테스트]: #test-app
  [GetStartedWithData 앱]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [관리 포털]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-js
  [인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-js
  [푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-js-vs2012
