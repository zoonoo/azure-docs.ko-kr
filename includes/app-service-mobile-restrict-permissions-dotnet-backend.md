

기본적으로 모바일 앱에서 정의된 끝점은 공개적으로 노출됩니다. 리소스를 보안하려면 인증된 클라이언트만 액세스를 허용해야 합니다.

1. Visual Studio에서 해당 모바일 앱 코드가 포함된 프로젝트를 엽니다. 

2. 솔루션 탐색기에서 컨트롤러 폴더를 확장하고 TodoItemController.cs 프로젝트 파일을 엽니다.

	**TodoItemController** 클래스는 TodoItem 테이블에 대한 데이터 액세스를 구현합니다.

3. **TodoItemController** 클래스에 `Authorize` 특성을 적용합니다.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	그러면 **TodoItem** 테이블에 대한 모든 작업에 인증된 사용자가 필요합니다.

	>[AZURE.NOTE]개별 메서드에 Authorize 특성을 적용하여 컨트롤러에 표시된 메서드에서 특정 권한 수준을 설정합니다.

4. 모바일 앱 프로젝트를 다시 게시합니다.

<!---HONumber=August15_HO6-->