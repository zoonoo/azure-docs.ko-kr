<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정
=====================================================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

이 항목에서는 Azure 모바일 서비스에서 서버 스크립트를 활용하는 방법을 보여 줍니다. 서버 스크립트는 모바일 서비스에서 등록되며 삽입 및 업데이트할 데이터에 대해 유효성 검사 및 데이터 수정을 포함한 다양한 작업을 수행하는 데 사용할 수 있습니다. 이 자습서에서는 데이터의 유효성을 검사하고 데이터를 수정하는 서버 스크립트를 정의하고 등록합니다. 서버 쪽 스크립트의 동작이 클라이언트에 영향을 미치는 경우가 많기 때문에 이런 새로운 동작을 활용하도록 HTML 앱도 업데이트합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [문자열 길이 유효성 검사 추가](#string-length-validation)
2.  [유효성 검사를 지원하도록 클라이언트 업데이트](#update-client-validation)
3.  [삽입 시 타임스탬프 추가](#add-timestamp)
4.  [타임스탬프를 표시하도록 클라이언트 업데이트](#update-client-timestamp)

이 자습서는 이전 자습서인 [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-html)의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-html)을 완료해야 합니다.

유효성 검사 추가
----------------

사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 먼저, 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부하는 스크립트를 등록합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][0]

2.  **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][1]

3.  **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

   	![][2]

4.  기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, {
                     error: "Text cannot exceed 10 characters"
                 });
             } else {
                 request.execute();
             }
         }

    이 스크립트는 **TodoItem.text** 속성의 길이를 확인하여 길이가 10자를 초과하는 경우 오류 응답을 보냅니다. 그렇지 않으면 **execute** 함수가 호출되어 삽입이 완료됩니다.

    **참고**

    **지우기**를 클릭한 후 **저장**을 클릭하여 **스크립트** 탭에서 등록된 스크립트를 제거할 수 있습니다.

클라이언트 업데이트
-------------------

모바일 서비스가 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다.

1.  [Get started with data](/en-us/develop/mobile/tutorials/get-started-with-data-html) 자습서를 완료할 때 수정한 프로젝트의 **server** 하위 폴더에서 다음 명령 파일 중 하나를 실행합니다.

    -   **launch-windows**(Windows 컴퓨터)
    -   **launch-mac.command**(Mac OS X 컴퓨터)
    -   **launch-linux.sh**(Linux 컴퓨터)

    **참고**

    Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 'R'을 입력하십시오. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.

    앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

2.  app.js 파일을 연 다음 **\$('\#add-item').submit()** 이벤트 처리기를 다음 코드로 바꿉니다.

         $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  웹 브라우저에서 <http://localhost:8000/>으로 이동한 다음 **새 작업 추가**에 텍스트를 입력한 다음 **추가**를 클릭합니다.

   	작업이 실패하고 오류 처리 중에 대화 상자에 오류 응답이 표시됩니다.

타임스탬프 추가
---------------

이전 작업에서는 삽입의 유효성을 검사하여 삽입을 허용하거나 거부했습니다. 이제, 삽입되기 전에 개체에 타임스탬프 속성을 추가하는 서버 스크립트를 사용하여 삽입된 데이터를 업데이트합니다.

**참고**

여기 나온 **createdAt** 타임스탬프 속성은 이제 중복됩니다. 모바일 서비스는 각 테이블의 **\_\_createdAt** 시스템 속성을 자동으로 만듭니다.

1.  [관리 포털](https://manage.windowsazure.com/)의 **스크립트** 탭에서 현재 **삽입** 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, {
                     error: 'Text length must be under 10'
                 });
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    이 함수는 **request**.**execute** 호출에 의해 삽입되기 전에 개체에 새 **createdAt** 타임스탬프 속성을 추가하여 이전 삽입 스크립트를 보강합니다.

    **참고**

    이 삽입 스크립트를 처음 실행할 때는 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정하면 첫 번째 실행 시 모바일 서비스가 **createdAt** 열을 **TodoItem** 테이블에 자동으로 추가합니다. 동적 스키마는 기본적으로 새 모바일 서비스에 대해 사용하도록 설정되어 있으며 앱을 게시하기 전에 이 스키마를 사용하지 않도록 설정해야 합니다.

2.  웹 브라우저에서 페이지를 다시 로드한 다음 **새 작업 추가**에 텍스트를 10자 미만으로 입력하고 **추가**를 클릭합니다.

   	새 타임스탬프는 앱 UI에서 표시되지 않습니다.

3.  관리 포털로 돌아가서 **todoitem** 테이블에서 **찾아보기** 탭을 클릭합니다.

   	이제 **createdAt** 열이 생겼고 새로 삽입된 항목에 타임스탬프 값이 추가되었습니다.

다음으로, 이 새 열을 표시하도록 앱을 업데이트해야 합니다.

클라이언트 다시 업데이트
------------------------

모바일 서비스 클라이언트는 정의 형식에 대한 속성으로 직렬화할 수 없는 응답 데이터를 무시합니다. 마지막 단계는 이 새 데이터를 표시하도록 클라이언트를 업데이트하는 것입니다.

1.  편집기에서 app.js 파일을 연 다음 **refreshTodoItems** 함수를 다음 코드로 바꿉니다.

         function refreshTodoItems() {
             var query = todoItemTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             });

             query.read().then(function (todoItems) {
                 var listItems = $.map(todoItems, function(item) {
                     return $('<li>')
                         .attr('data-todoitem-id', item.id)
                         .append($('<button class="item-delete">Delete</button>'))
                         .append($('<input type="checkbox" class="item-complete">')
                             .prop('checked', item.complete))
                         .append($('<div>').append($('<input class="item-text">').val(item.text))
                         .append($('<span class="timestamp">' 
                             + (item.createdAt && item.createdAt.toDateString() + ' '
                             + item.createdAt.toLocaleTimeString() || '') 
                             + '</span>')));

                 });

                 $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                 $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
             });
         }

   	새 **createdAt** 속성의 데이터 부분이 표시됩니다. 

2.  편집기에서 style.css 파일을 열고 `item-text` 클래스의 스타일을 다음과 같이 바꿉니다.

         .item-text { width: 70%; height: 26px; line-height: 24px; 
             border: 1px solid transparent; background-color: transparent; }
         .timestamp { width: 30%; height: 40px; font-size: .75em; }

    텍스트 상자의 크기가 조정되고 새 타임스탬프 텍스트의 스타일이 지정됩니다.

3.  페이지를 다시 로드합니다.

   	타임스탬프는 삽입 스크립트를 업데이트한 후 삽입된 항목에만 표시됩니다.

4.  다시 **refreshTodoItems** 함수에서 쿼리를 정의하는 코드 줄을 다음과 같이 바꿉니다.

          var query = todoItemTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             });

   	또한 이 함수는 쿼리를 업데이트하여 타임스탬프 값이 없는 항목을 필터링합니다.

5.  페이지를 다시 로드합니다.

   	타임스탬프 값 없이 생성된 모든 항목이 UI에서 사라집니다.

데이터 작업에 대한 이 자습서를 완료했습니다.

다음 단계
---------

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화](/en-us/develop/mobile/tutorials/add-paging-to-data-html)를 검토해 보십시오.

자세한 내용은 [서버 스크립트 작업](/en-us/develop/mobile/how-to-guides/work-with-server-scripts) 및 [모바일 서비스 HTML/JavaScript 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)를 참조하십시오.

<!-- Anchors. -->
[Add string length validation]: #string-length-validation
[Update the client to support validation]: #update-client-validation
[Add a timestamp on insert]: #add-timestamp
[Update the client to display the timestamp]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png