이 섹션에서는 이름이 **CompleteDate**인 새 타임스탬프 필드를 추가하여 데이터베이스의 모델을 수정합니다. 이 필드는 todo 항목이 완료된 마지막 시간을 기록합니다. Entity Framework는 [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621)에서 파생된 기본 데이터베이스 이니셜라이저 클래스를 사용하는 모델 변경에 따라 데이터베이스를 업데이트합니다. 

1. Visual Studio의 솔루션 탐색기에서 todolist 서비스 프로젝트의 **App_Start** 폴더를 확장합니다. WebApiConfig.cs 파일을 엽니다.

2. WebApiConfig.cs 파일에서 기본 데이터베이스 이니셜라이저 클래스는 `DropCreateDatabaseIfModelChanges` 클래스에서 파생됩니다. 즉, 모델을 변경하면 테이블이 삭제되고 새 모델을 수용하는 테이블이 다시 생성됩니다. 테이블의 데이터가 손실되고 테이블이 다시 시드됩니다. 초기값 데이터가 다음과 같도록 데이터베이스 이니셜라이저의 Seed 메서드를 수정하고 WebApiConfig.cs 파일을 저장합니다.

    >[WACOM.NOTE] 기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때마다 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법](/ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations)을 참조하세요.

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. Visual Studio의 솔루션 탐색기에서 todolist 서비스 프로젝트의 **DataObjects** 폴더를 확장합니다. TodoItem.cs 파일을 열고 다음과 같이 CompleteDate 필드가 포함되도록 TodoItem 클래스를 업데이트합니다. 그런 다음 TodoItem.cs 파일을 저장합니다.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. Visual Studio의 솔루션 탐색기에서 todolist 서비스 프로젝트의 **Controllers** 폴더를 확장합니다. TodoItemController.cs 파일을 열고**Complete** 속성이 False에서 True로 변경되면 **CompleteDate**를 설정하도록 `PatchTodoItem` 메서드를 업데이트합니다. 그런 다음 TodoItemController.cs 파일을 저장합니다.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. todolist .NET 백 엔드 서비스 프로젝트를 다시 빌드하고, 빌드 오류가 없는지 확인합니다. 

그런 다음, 새 **CompleteDate** 데이터를 표시하도록 클라이언트 앱을 업데이트합니다.
