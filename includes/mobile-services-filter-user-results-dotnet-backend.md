TodoItem 테이블의 데이터에 액세스하는 데 인증이 필요하므로 모바일 서비스에서 할당한 userID 값을 사용하여 반환된 데이터를 필터링할 수 있습니다.

> [WACOM.NOTE]아래 메서드에는 **User**의 **Authorizationlevel**에서 적용된 **AuthorizeLevel** 특성이 있습니다. 그러면 테이블 액세스가 인증된 사용자로만 제한됩니다.

1.  Visual Studio 2013에서 모바일 서비스 프로젝트를 열고 DataObjects 폴더를 확장한 후 TodoItem.cs 프로젝트 파일을 엽니다.

    TodoItem 클래스가 데이터 개체를 정의합니다. 필터링에 사용하려면 UserId 속성을 추가해야 합니다.

2.  다음과 같은 새 UserId 속성을 **TodoItem** 클래스에 추가합니다.

        public string UserId { get; set; }

    > [WACOM.NOTE] 기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때마다 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. Azure에서는 SQL 데이터베이스에 대해 기본 이니셜라이저를 사용할 수 없습니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법][Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법]을 참조하세요.

3.  솔루션 탐색기에서 Controllers 폴더를 확장하고 TodoItemController.cs 프로젝트 파일을 열어 다음 **using** 문을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service.Security;

    **TodoItemController** 클래스는 TodoItem 테이블에 대한 데이터 액세스를 구현합니다.

4.  **PostTodoItem** 메서드를 찾아 이 메서드의 시작 부분에 다음 코드를 추가합니다.

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        // Set the user ID on the item.
        item.UserId = currentUser.Id;

    이 코드는 항목에 UserId 값을 추가합니다. 이 값은 TodoItem 테이블에 삽입되기 전에 인증된 사용자의 사용자 ID입니다.

5.  **GetAllTodoItems** 메서드를 찾아 기존 **return** 문을 다음 코드 줄로 바꿉니다.

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

    이 쿼리는 반환된 TodoItem 개체를 필터링하여 각 사용자가 자신이 삽입한 항목만 수신하도록 합니다. 이 단계는 옵션입니다.

6.  Azure에 모바일 서비스 프로젝트를 다시 게시합니다.

  [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
