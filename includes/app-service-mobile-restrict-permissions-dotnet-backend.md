
기본적으로 모바일 앱 백 엔드의 API는 익명으로 호출할 수 있습니다. 다음으로, 인증된 클라이언트로만 액세스를 제한해야 합니다.

1. 사용자 PC의 Visual Studio에서 서버 프로젝트를 열고 **Controllers** > **TodoItemController.cs**로 이동합니다.

2. 다음과 같이 **TodoItemController** 클래스에 `[Authorize]` 특성을 추가합니다. 이 작업을 수행하려면 TodoItem 테이블에 대한 모든 작업을 인증된 사용자가 만들어야 합니다. 특정 메서드로만 액세스를 제한하기 위해 이 특성을 클래스 대신 해당 메서드에만 적용할 수도 있습니다.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    이 작업을 수행하려면 TodoItem 테이블에 대한 모든 작업을 인증된 사용자가 만들어야 합니다. 특정 메서드로만 액세스를 제한하기 위해 이 특성을 클래스 대신 해당 메서드에만 적용할 수도 있습니다.
   
3. 서버 프로젝트를 다시 게시합니다.

<!---HONumber=Oct15_HO3-->