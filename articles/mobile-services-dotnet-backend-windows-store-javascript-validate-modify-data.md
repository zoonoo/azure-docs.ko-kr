<properties 
	pageTitle=".Net 백 엔드를 사용하여 데이터 유효성 검사 및 수정(Windows 스토어) | 모바일 개발자 센터" 
	description=".Net 백 엔드 Microsoft Azure 모바일 서비스를 사용하여 Javascript Windows 스토어 앱에 대한 데이터의 유효성을 검사하고 데이터를 수정 및 보강하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>

# .NET 백 엔드를 사용하여 모바일 서비스에서 데이터 유효성 검사 및 수정

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

이 항목에서는 .Net 백 엔드 Azure 모바일 서비스에서 코드를 사용하여 데이터를 유효성 검사 및 수정하는 방법을 보여 줍니다. .NET 백 엔드 서비스는 Web API 프레임워크에 기본 제공되는 HTTP 서비스입니다. Web API 프레임워크로 정의되는  `ApiController` 클래스에 대해 잘 알고 있다면 모바일 서비스에 제공되는  `TableController` 클래스는 쉽게 이해할 수 있습니다.  `TableController`는  `ApiController` 클래스에서 파생되며 데이터베이스 테이블 연결을 위한 추가 기능을 제공합니다. 이 클래스는 이 자습서에서 다루는 유효성 검사 및 데이터 수정을 비롯하여 삽입 및 업데이트 중인 데이터에 대한 작업을 수행하는 데 사용될 수 있습니다. 

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [문자열 길이 유효성 검사 추가]
2. [유효성 검사를 지원하도록 클라이언트 업데이트]
3. [길이 유효성 검사 테스트]
4. [CompleteDate에 대한 타임스탬프 필드 추가]
5. [CompleteDate를 표시하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [시작] 또는 [데이터 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/)의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [시작] 또는 [데이터 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/) 자습서를 완료해야 합니다.  

## <a name="string-length-validation"></a>모바일 서비스에 유효성 검사 코드 추가

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 잘못된 텍스트 길이에 대한 오류 응답을 보내도록 설정되어 있으므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다. 오류는  `IMobileServiceTable<TodoItem].InsertAsync()`에 대한 클라이언트 앱 호출에서 발생됩니다.

1. Visual Studio의 솔루션 탐색기에서 JavaScript 클라이언트 프로젝트로 이동하여 **js** 폴더를 확장합니다. 그런 다음 default.js 파일을 엽니다.

2. default.js에서 기존 **insertTodoItem** 함수를 다음 함수 정의로 바꿉니다.


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoIte
	ms.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	이 함수 버전은 오류 처리를 포함하며 응답, 상태 텍스트 및 상태 코드의 오류 메시지와 함께  `MessageDialog`를 표시합니다.

## <a name="test-length-validation"></a>길이 유효성 검사 테스트

1. Visual Studio의 솔루션 탐색기에서 JavaScript 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **디버그**, **새 인스턴스 시작**을 클릭합니다.

2. 새 todo 항목에 길이가 10자를 초과하는 텍스트를 입력한 다음 **저장**을 클릭합니다.

    ![][1]

3. 잘못된 텍스트에 대한 응답으로 다음과 비슷한 메시지 대화 상자가 표시됩니다.

    ![][2]

## <a name="add-timestamp"></a>CompleteDate에 대한 타임스탬프 필드 추가


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>completeDate를 표시하도록 클라이언트 업데이트

마지막 단계는 이 새 **completeDate** 데이터를 표시하도록 클라이언트를 업데이트하는 것입니다. 


1. Visual Studio용 솔루션 탐색기의 JavaScript 클라이언트 프로젝트에서 default.html 파일을 엽니다. 바인딩 템플릿  `div` 태그 요소를 아래 정의로 바꿉니다. 그런 다음 파일을 저장합니다. 이 정의는 **completeDate**에 바인딩된 innerText 속성과 함께  `div` 태그를 추가합니다.
	      
        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>



2. default.js에서 완료된 todoitem이 결과에 포함되도록 기존 **refreshTodoItems** 함수에서  `.Where` 절 함수를 제거합니다.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
                    });
            };


3. default.js에서 업데이트 이후에 항목을 새로 고치고 완료된 항목을 목록에서 제거하지 않도록 **updateCheckedTodoItem** 함수를 다음과 같이 업데이트합니다. 그런 다음 파일을 저장합니다.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. Visual Studio의 솔루션 탐색기 창에서 **솔루션**을 마우스 오른쪽 단추로 클릭하고 **솔루션 다시 빌드**를 클릭하여 클라이언트와 .NET 백 엔드 서비스를 모두 다시 빌드합니다. 두 프로젝트가 모두 오류 없이 빌드되는지 확인합니다.

    ![][3]
	
5. **F5** 키를 눌러 클라이언트 앱 및 서비스를 로컬로 실행합니다. 새 항목을 추가하고 일부 항목을 완료 표시하여 업데이트되는 **CompleteDate** 타임스탬프를 확인합니다.

    ![][4]

6. Visual Studio의 솔루션 탐색기에서 todolist 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. Azure 포털에서 다운로드한 게시 설정 파일을 사용하여 .NET 백 엔드 서비스를 Microsoft Azure에 게시합니다.

7. 모바일 서비스 주소에 대한 연결 주석 처리를 제거하여 클라이언트 프로젝트에 대한 default.js 파일을 업데이트합니다. Azure 계정에 호스트된 .NET 백 엔드를 기준으로 앱을 테스트합니다.




## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화]를 계속 진행하세요.

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 다음 자습서를 참조하세요.

* [사용자의 서비스 쪽 권한 부여]
  <br/>인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.

<!-- Anchors. -->
[문자열 길이 유효성 검사 추가]: #string-length-validation
[유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
[길이 유효성 검사 테스트]: #test-length-validation
[CompleteDate에 대한 타임스탬프 필드 추가]: #add-timestamp
[CompleteDate를 표시하도록 클라이언트 업데이트]: #update-client-timestamp
[다음 단계]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[사용자의 서비스 쪽 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
[시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript 및 HTML]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-js

[관리 포털]: https://manage.windowsazure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
