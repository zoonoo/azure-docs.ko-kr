

1. Visual Studio에서 Controllers 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 확장한 후 **새 스캐폴드 항목**을 클릭합니다. 그러면 스캐폴드 추가 대화 상자가 표시됩니다.

2. **Azure 모바일 서비스**를 확장하고 **Azure 모바일 서비스 사용자 지정 컨트롤러**를 클릭한 후 **추가**를 클릭하고 **컨트롤러 이름**을  `CompleteAllController`로 입력한 후 **추가**를 다시 클릭합니다.

	![Web API 스캐폴드 추가 대화상자](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	그러면 이름이 **CompleteAllController**인 빈 컨트롤러 클래스가 새로 생성됩니다.

	>[AZURE.NOTE]대화 상자에 모바일 서비스별 스캐폴드가 없는 경우 새 **Web API 컨트롤러 - 비어 있음**이 생성됩니다. 이 새 컨트롤러 클래스에서 **Services** 속성을 추가하면 **ApiServices** 유형이 반환됩니다. 이 속성은 컨트롤러 내부에서 서버별 설정에 액세스하는 데 사용됩니다.

3. **CompleteAllController.cs**에서 다음 **using** 문을 추가합니다. 	 `todolistService`를 모바일 서비스 프로젝트의 네임스페이스로 바꿉니다. 그러면  `Service`가 추가된 모바일 서비스 이름이 됩니다.

		using System.Threading.Tasks;
		using todolistService.Models;

4. **CompleteAllController.cs**에서 다음 클래스를 추가하여 클라이언트로 전송되는 응답을 래핑합니다.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. 새 컨트롤러에 다음 코드를 추가합니다.  `todolistContext`를 데이터 모델의 DbContext 이름으로 바꿉니다. 그러면  `Context`와 함께 추가된 모바일 서비스 이름이 됩니다. 마찬가지로 UPDATE 문의 스키마 이름을 모바일 서비스 이름으로 바꿉니다. 이 코드에서는 **TodoItems** 테이블에 직접 액세스하여 모든 항목에 완료 플래그를 설정하는 데 [Database 클래스](http://msdn.microsoft.com/library/system.data.entity.database.aspx)를 사용합니다. 이 메서드는 POST 요청을 지원하며, 변경된 행의 수가 정수 값으로 클라이언트에 반환됩니다.


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.NOTE] 기본 권한이 있는 사용자는 누구나 앱 키를 사용하여 사용자 지정 API를 호출할 수 있습니다. 그러나 응용 프로그램 키는 안전하게 배포되거나 저장되지 않을 수 있기 때문에 보안 자격 증명으로 간주되지 않습니다. 보안을 강화하기 위해 인증된 사용자에게만 액세스를 허용하는 것이 좋습니다.

<!--HONumber=47-->
