

1. 그런 후에 다음 코드 줄을 추가하거나 주석 처리를 제거하고, `<yourClient>`은(는) 모바일 서비스에 프로젝트를 연결할 때 service.js 파일에 추가한 변수로 바꿉니다.

		var todoTable = <yourClient>.getTable('TodoItem');

   	이 코드는 캐싱 필터를 사용하여 새 데이터베이스 테이블에 대한 프록시 개체(**todoTable**)를 만듭니다.

2. **InsertTodoItem** 함수를 다음 코드로 바꿉니다.

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	이 코드는 테이블에 새 항목을 삽입합니다.

3. **RefreshTodoItems** 함수를 다음 코드로 바꿉니다.

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	모바일 서비스에서 반환된 모든 **TodoItem** 개체가 포함된 todoTable의 항목 컬렉션에 대한 바인딩이 설정됩니다.

4. **UpdateCheckedTodoItem** 함수를 다음 코드로 바꿉니다.
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	항목 업데이트가 모바일 서비스로 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

<!--HONumber=54-->