

1. Visual Studio에서 **데이터 시작** 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2. **F5** 키를 눌러 앱을 실행한 후 **TodoItem 삽입**에 텍스트를 입력하고 **저장**을 클릭합니다.

3. TodoItem 테이블에 저장된 항목이 네 개 이상이므로 이전 단계를 세 번 이상 반복합니다. 

2. default.js 파일에서 기존 **RefreshTodoItems** 메서드를 다음 코드로 바꿉니다.

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

  	이 쿼리를 데이터 바인딩 중 실행하면 완료로 표시되지 않은 맨 위에 있는 세 개의 항목이 반환됩니다.

3. **F5** 키를 눌러 앱을 실행합니다.

  	TodoItem 테이블에서 처음 세 개의 결과만 표시됩니다. 

4. (옵션) 브라우저 개발자 도구 또는 [Fiddler](영문)와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 봅니다. 

   	**take(3)** 메서드는 쿼리 URI에서 쿼리 옵션 **$top=3**으로 변환되었습니다.

5. **RefreshTodoItems** 메서드를 다음 코드로 한 번 더 업데이트합니다.
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

   	이 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

    <div class="dev-callout"><b>참고</b>
    <p>이 자습서에서는 하드 코드된 페이징 값을 <strong>Take</strong> 및 <strong>Skip</strong> 메서드에 전달하여 간소화된 시나리오를 사용합니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다.  <strong>includeTotalCount</strong> 메서드를 호출하여 서버에서 사용 가능한 총 항목 수를 페이징 데이터와 함께 가져올 수도 있습니다.</p>
    </div>

6. (옵션) 모바일 서비스에 전송된 요청의 URI를 다시 봅니다. 

   	**skip(3)** 메서드는 쿼리 URI에서 쿼리 옵션 **$skip=3**으로 변환되었습니다.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
