
1. MainPage.xaml.cs 파일에서 다음 using 문을 추가하거나 주석 처리를 제거합니다. 

		using Microsoft.WindowsAzure.MobileServices;

2. TodoItem 클래스 정의를 다음 코드로 바꿉니다.

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	**JsonPropertyAttribute**는 클라이언트 유형의 속성 이름과 기본 데이터 테이블의 열 이름 간 매핑을 정의하는 데 사용됩니다.

	>[AZURE.NOTE]범용 Windows 앱 프로젝트에서 TodoItem 클래스는 공유 DataModel 폴더의 별도 코드 파일에서 정의됩니다.

3. MainPage.xaml.cs에서 기존 항목 컬렉션을 정의하는 줄을 주석 처리하거나 삭제한 후 다음 줄을 추가하거나 주석 처리를 제거합니다. 이때 _&lt;yourClient&gt;_는 모바일 서비스에 프로젝트를 연결할 때 App.xaml.cs 파일에 추가한 `MobileServiceClient` 필드로 바꿉니다.

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	이 코드는 데이터베이스 테이블(todoTable)에 대한 모바일 서비스 인식 바인딩 컬렉션(항목) 및 프록시 클래스를 만듭니다.

4. **InsertTodoItem** 메서드에서 **TodoItem.Id** 속성을 설정하는 코드 줄을 제거하고 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

		await todoTable.InsertAsync(todoItem);


	이 코드는 테이블에 새 항목을 삽입합니다.

5. **RefreshTodoItems** 메서드를 다음 코드로 바꿉니다.

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	모바일 서비스에서 반환된 모든 **TodoItem** 개체가 포함된 `todoTable`의 항목 컬렉션에 대한 바인딩이 설정됩니다. 쿼리 실행 시 문제가 발생하면 메시지 상자에 오류가 표시됩니다.

6. **UpdateCheckedTodoItem** 메서드에서 **async** 한정자를 메서드에 추가한 후 다음 코드 줄의 주석 처리를 제거합니다.

		await todoTable.UpdateAsync(item);

	항목 업데이트가 모바일 서비스로 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.
<!--HONumber=54-->