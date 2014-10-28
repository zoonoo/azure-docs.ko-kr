## <a name="update-app"></a>사용자 지정 API를 호출하도록 앱 업데이트

1.  Visual Studio에서 퀵 스타트 프로젝트의 default.html 파일을 열고 이름이 `buttonRefresh`인 **button** 요소를 찾아 바로 뒤에 다음과 같은 새 요소를 추가합니다.

        <button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

    이 코드는 페이지에 새 단추를 추가합니다.

2.  `js` 프로젝트 폴더의 default.js 코드 파일을 열고 **refreshTodoItems** 함수를 찾아 이 함수에 다음 코드가 포함되었는지 확인합니다.

        todoTable.where({ complete: false })
           .read()
           .done(function (results) {
               todoItems = new WinJS.Binding.List(results);
               listItems.winControl.itemDataSource = todoItems.dataSource;
           });            

    이 코드는 완료된 항목이 쿼리에서 반환되지 않도록 항목을 필터링합니다.

3.  **refreshTodoItems** 함수 뒤에 다음 코드를 추가합니다.

        var completeAllTodoItems = function () {
            var okCommand = new Windows.UI.Popups.UICommand("OK");

            // Asynchronously call the custom API using the POST method. 
            mobileService.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done(function () {
                    refreshTodoItems();
                });
            }, function (error) {
                var dialog = new Windows.UI.Popups
                    .MessageDialog(error.message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done();
            });
        };

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

    이 메서드는 새 단추의 **Click** 이벤트를 처리합니다. **InvokeApiAsync**(영문) 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다.

## <a name="test-app"></a>앱 테스트

1.  Visual Studio에서 **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

2.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

3.  목록에 todo 항목을 여러 개 추가할 때까지 이전 단계를 반복합니다.

4.  **Complete All** 단추를 클릭합니다.

    ![][]

    완료 표시된 항목 수를 나타내는 메시지 대화 상자가 표시되고 필터링된 쿼리가 다시 실행되어 목록에서 모든 항목을 지웁니다.

  []: ./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png
