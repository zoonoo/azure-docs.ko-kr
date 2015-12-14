
기본적으로 모바일 앱 백 엔드의 API는 익명으로 호출할 수 있습니다. 다음으로, 인증된 클라이언트로만 액세스를 제한해야 합니다.

+ **(포털을 통해) Node.js 백 엔드**:  
	
	모바일 앱의 **설정**에서 **Easy Table**을 클릭하고 테이블을 선택합니다. **사용 권한 변경**을 클릭하고 모든 사용 권한에 대해 **인증된 액세스만**을 선택한 다음 **저장**합니다.

+ **.NET 백 엔드(C#)**:

	서버 프로젝트에서 **컨트롤러** > **TodoItemController.cs**로 이동합니다. 다음과 같이 **TodoItemController** 클래스에 `[Authorize]` 특성을 추가합니다. 특정 메서드로만 액세스를 제한하기 위해 이 특성을 클래스 대신 해당 메서드에만 적용할 수도 있습니다. 서버 프로젝트를 다시 게시합니다.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **(Node.js 코드를 통해) Node.js 백 엔드**:
	
	테이블 액세스에 대한 인증을 요청하려면 Node.js 서버 스크립트에 다음 줄을 추가합니다.


        table.access = 'authenticated';

	자세한 내용은 [Azure 모바일 앱 Node.js SDK를 사용 하는 방법](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md)에서 [테이블에 대한 액세스 인증 요구](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->