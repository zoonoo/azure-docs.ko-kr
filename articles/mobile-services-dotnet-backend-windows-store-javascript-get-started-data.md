<properties urlDisplayName="Get Started with Data" pageTitle="데이터 작업 시작(Windows 스토어) | 모바일 개발자 센터" metaKeywords="" description="모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# 기존 앱에 모바일 서비스 추가

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

이 항목에서는 Windows 스토어 앱용 백 엔드 데이터 소스로 Azure 모바일 서비스를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 2013 프로젝트를 다운로드하고, 새 모바일 서비스를 만들고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

이 자습서에서 만들 모바일 서비스는 .NET 백 엔드 모바일 서비스입니다. .NET 백 엔드를 사용하면 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있으며, 로컬 컴퓨터에서 모바일 서비스를 실행 및 디버그할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 JavaScript 백 엔드 버전을 참조하세요.

>[WACOM.NOTE]이 항목에서는 Windows 스토어 프로젝트에 Azure 모바일 서비스를 추가하는 방법을 보여 줍니다. Visual Studio 2013 도구를 사용하여 동일한 .NET 백 엔드 모바일 서비스를 범용 Windows 앱 프로젝트에 추가할 수 있습니다. 자세한 내용은 이 자습서의 [범용 Windows 앱 버전](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data)을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Windows 스토어 앱 프로젝트 다운로드]
2. [새 모바일 서비스 만들기]
3. [로컬로 모바일 서비스 다운로드]
4. [모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트]
5. [로컬에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]
6. [Azure에 모바일 서비스 게시]
7. [Azure에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F)을 참조하세요.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. 무료 평가판을 이용할 수 있습니다.

##<a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 Visual Studio 2013의 Windows 스토어 앱 프로젝트인 [GetStartedWithMobileServices 앱][개발자 코드 샘플 사이트]을 기반으로 합니다. 이 앱의 UI는 추가된 항목이 로컬 메모리에 저장된다는 점을 제외하고 모바일 서비스 빠른 시작에서 생성된 앱과 유사합니다. 

1. [개발자 코드 샘플 사이트]에서 GetStartedWithMobileServices 샘플 앱의 JavaScript 버전을 다운로드합니다. 

2. Visual Studio 2013을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭하여 Visual Studio를 관리자 권한으로 실행합니다.

3. Visual Studio 2013에서 다운로드한 프로젝트를 엽니다. 솔루션 탐색기에서 js 폴더를 확장하고 default.js 파일을 검토합니다.

   	추가된 **TodoItem** 개체가 메모리 내 `WinJS.Binding.List`에 저장됩니다.

4. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

5. **TodoItem 삽입** 아래의 상자에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][0]  

   	저장된 텍스트가 **데이터 쿼리 및 업데이트** 아래 두 번째 열에 표시됩니다.

##<a name="create-service"></a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>모바일 서비스 프로젝트 다운로드 및 솔루션에 추가

1. [Azure 관리 포털]에서 새 모바일 서비스 또는 해당 클라우드 아이콘 탭을 클릭하여 개요 페이지로 이동합니다.

    ![][2]

2. **Windows 스토어** 플랫폼을 클릭합니다. **시작** 섹션 아래에서 **기존 Windows 스토어 앱 연결**을 클릭하고 **다운로드** 단추를 클릭하여 모바일 서비스의 개인화된 시작 프로젝트를 다운로드합니다. 

    ![][3]

3. **시작** 섹션의 아래쪽으로 스크롤하여 **클라우드에 서비스 게시**라는 단계로 이동합니다. 아래의 스크린샷에 보이는 링크를 클릭하여 방금 다운로드한 모바일 서비스용 게시 프로필 파일을 다운로드합니다.

    > [WACOM.NOTE] Azure 계정과 관련된 중요한 정보가 포함되어 있으므로 파일을 안전한 장소에 보관하세요. 이 자습서 뒤쪽에서 모바일 서비스를 게시한 후 이 파일을 삭제할 것입니다. 

    ![][5]


4. 방금 다운로드한 개인화된 서비스 시작 프로젝트의 압축을 풉니다. 압축 파일에 있던 폴더를 Getting Started with Data 솔루션 파일(.sln)이 있는 **JavaScript** 디렉터리로 복사합니다. 이렇게 하면 NuGet 패키지 관리자에서 모든 패키지의 동기화를 더욱 쉽게 유지할 수 있습니다. 

    ![][26]

5. Visual Studio의 솔루션 탐색기에서 Getting Started with Data Windows 스토어 응용 프로그램용 솔루션을 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.

    ![][4]

6. 기존 프로젝트 추가 대화 상자에서, **JavaScript** 디렉터리로 이동한 모바일 서비스 프로젝트 폴더를 찾습니다. 서비스 하위 디렉터리에서 C# 프로젝트 파일(.csproj)을 선택합니다. **열기**를 클릭하여 프로젝트를 솔루션에 추가합니다.

    ![][6]

7. Visual Studio의 솔루션 탐색기에서, 방금 추가한 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 클릭하여 오류 없이 빌드되는지 확인합니다. 빌드 중에, 프로젝트에서 참조된 일부 NuGet 패키지를 NuGet 패키지 관리자에서 복원해야 할 수 있습니다.

    ![][20]

8. 서비스 프로젝트를 다시 마우스 오른쪽 단추로 클릭합니다. 이번에는 **디버그** 상황에 맞는 메뉴에서 **새 인스턴스 시작**을 클릭합니다.

    ![][21]

    Visual Studio에서 서비스용 기본 웹 페이지가 열립니다. 기본 웹 페이지에서 모바일 서비스의 메서드를 테스트하려면 **지금 시도**를 클릭할 수 있습니다.

    ![][22]

    Visual Studio에서는 기본적으로 IIS Express에 로컬로 모바일 서비스를 호스트합니다. 작업 표시줄에서 IIS Express용 트레이 아이콘을 마우스 오른쪽 단추로 클릭하여 확인할 수 있습니다.

    ![][23]


#<a name="update-app"></a>모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트

이 섹션에서는 모바일 서비스를 응용 프로그램용 백 엔드 서비스로 사용하기 위해 Windows 스토어 앱을 업데이트합니다.


1. Visual Studio의 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

    ![][7]

2. NuGet 패키지 관리 대화 상자의 온라인 패키지 컬렉션에서 **WindowsAzure.MobileServices.WinJS**를 검색하고 클릭하여 Azure 모바일 서비스 Nuget 패키지를 설치합니다. 그런 다음 대화 상자를 닫습니다.

    ![][8]

3. 다시 Azure 관리 포털로 돌아와 모바일 서비스의 개요 페이지에서 **앱 연결 및 서비스에 데이터 저장**이라는 레이블의 단계를 찾습니다. 언어로 **JavaScript**를 클릭하고 `MobileServiceClient`를 만들기 위한 코드 조각을 복사합니다.

    ![][9]

4. Visual Studio의 솔루션 탐색기에서**js** 폴더를 확장하고 default.js를 엽니다. 방금 복사한 코드 조각을 `app.onactivated` 이벤트 처리기의 `todoItems` 변수 바로 앞에 붙여넣습니다. 이 코드 조각에서는 응용 프로그램 키를 사용하여 Azure에서 모바일 서비스에 연결하기 위한 생성자가 주석 처리되어 있습니다. 다음 단계에서는 이 주석 처리를 제거합니다.

    ![][10]


5. default.js에서 `app.onactiviated` 이벤트 처리기에 대한 코드의 나머지 부분을 다음 코드로 교체합니다. 다음 코드는 `todoItems` 및 모바일 서비스로 테스트할 작업을 정의합니다. 그런 다음 파일을 저장합니다.

    이 코드는 앱이 데이터를 로컬의 메모리 내 대신 서비스에서 제공하는 테이블에 저장하도록 JavaScript용 모바일 서비스 SDK를 사용합니다. 세 가지 기본 메서드는 `insertTodoItem`, `refreshTodoItems` 및 `updateCheckedTodoItem`입니다. 이 세 가지 메서드를 사용하면 데이터 컬렉션을 Azure의 테이블에 비동기로 삽입하고 쿼리하며 업데이트할 수 있습니다.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. Visual Studio의 솔루션 탐색기에서 default.html 파일을 엽니다. 파일 맨 위에 MobileServices.js의 새로운 WinJS 스크립트 참조를 추가한 다음 파일을 저장합니다.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>로컬로 호스트한 서비스에 대해 Windows 스토어 앱 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Azure에 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Azure에 게시된 모바일 서비스 테스트

1. Visual Studio의 솔루션 탐색기에서 **js** 폴더를 확장하고 default.js를 엽니다.  로컬로 호스트한 모바일 서비스에 연결되는 `MobileServiceClient`를 만드는 코드를 주석으로 처리합니다. Azure의 서비스에 연결되는 `MobileServiceClient`를 만드는 코드의 주석 처리를 제거합니다. 파일 변경 사항을 저장합니다.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. Visual Studio에서 F5 키를 누르거나 디버그 메뉴에서 **디버깅 시작**을 클릭합니다. Azure에서 원격으로 호스트한 모바일 서비스에 연결하기 위해 앱을 실행하기 전에, 이전 변경 사항으로 Windows 스토어 앱이 다시 빌드됩니다. 

    ![][12]


3. 새 todoitem을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. 확인란을 클릭하여 몇몇 새 항목을 완료합니다. 각각의 새 todoItem이 저장되고, Azure 관리 포털에서 모바일 서비스에 대해 전에 구성한 SQL 데이터베이스에서 업데이트됩니다. 

    ![][16]

    변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 다시 시작할 수 있습니다. Azure 관리 포털 또는 Visual Studio의 SQL Server 개체 탐색기를 사용하여 데이터베이스를 검토할 수도 있습니다. 다음의 두 단계에서는 Azure 관리 포털을 사용하여 데이터베이스의 변경 사항을 확인합니다.

4. Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.

    ![][17]

5. 관리 포털에서 쿼리를 실행하여 Windows 스토어 앱에 의한 변경 사항을 확인합니다. 쿼리는 다음과 유사하지만, `todolist` 대신 데이터베이스 이름을 사용하게 됩니다.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

이제 **데이터 작업 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 이제 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료하세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 데이터를 변경하는 방법에 대해 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보세요.

* [인증 시작]
  <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>매우 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 HTML 및 JavaScript와 함께 사용하는 방법을 알아봅니다.
  
<!-- Anchors. -->

[Windows 스토어 앱 프로젝트 다운로드]: #download-app
[새 모바일 서비스 만들기]: #create-service
[로컬로 모바일 서비스 다운로드]: #download-the-service-locally
[모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트]: #update-app
[로컬에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]: #test-locally-hosted
[Azure에 모바일 서비스 게시]: #publish-mobile-service
[Azure에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]: #test-azure-hosted
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-js
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-js
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[개발자 코드 샘플 사이트]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library/
[MobileServiceClient 클래스]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  
