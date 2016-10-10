<properties
   pageTitle="Azure Functions를 사용하여 예약된 정리 작업 수행 | Microsoft Azure"
   description="Azure Functions를 사용하여 이벤트 타이머에 기반하여 실행되는 C# 함수를 만듭니다."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="glenga"/>
   
# Azure Functions을 사용하여 예약된 정리 작업 수행하기

이 토픽에서는 Azure Functions를 사용하여 데이터베이스 테이블에서 행을 정리하는 이벤트 타이머에 따라 실행하는 새로운 함수를 C#에서 만드는 방법을 알아봅니다. 새 함수는 Azure Functions 포털에서 미리 정의된 템플릿을 기반으로 생성됩니다. 이 시나리오를 지원하려면 문자열을 연결하는 데이터베이스를 함수 앱에서 앱 서비스 설정으로 설정해야 합니다.

## 필수 조건 

함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [무료 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.

이 토픽은 SQL Database의 *TodoItems* 테이블에서 대량 정리 작업을 실행하는 Transact-SQL 명령에 대해 보여줍니다. 이 TodoItems 테이블은 [Azure 앱 서비스 모바일 앱 퀵 스타트 자습서](../app-service-mobile/app-service-mobile-ios-get-started.md)를 완료하면 만들 수 있습니다. 샘플 데이터베이스도 사용할 수 있습니다. 다른 테이블을 사용하고자 할 경우, 명령을 수정해야 합니다.

포털의 모바일 앱 백엔드에서 사용한 연결 문자열은 **모든 설정** > **응용 프로그램 설정** > **연결 문자열** > **연결 문자열 값 표시** > **MS\_TableConnectionString**에서 가져올 수 있습니다. 또한, 포털의 SQL 데이터베이스에서 끌어온 연결 문자열은 **모든 설정** > **속성** > **데이터베이스 연결 문자열 표시** > **ADO.NET(SQL 인증)**에서 가져올 수 있습니다.

이 시나리오는 데이터베이스에 대한 대량 작업을 사용합니다. 함수가 모바일 앱 테이블에서 개별 CRUD 작업을 처리하게 하려면 모바일 테이블 바인딩을 사용해야 합니다.

## 함수 앱에서 SQL 데이터베이스 연결 문자열 설정

함수 앱은 Azure에서 함수 실행을 호스트합니다. 함수 앱 설정에 연결 문자열과 다른 비밀 정보를 저장하는 것이 좋습니다. 이렇게 하면 함소 코드가 리포지토리 어딘가에 있더라도 정보가 우연히 공개되지 않습니다.

1. [Azure Functions 포털](https://functions.azure.com/signin)로 이동하여 Azure 계정으로 로그인합니다.

2. 기존 함수 앱을 사용 하는 경우 **함수 앱**에서 선택하고 **열기**를 클릭합니다. 새 함수 앱을 만들려면 새 함수 앱에 대해 고유한 **이름**을 입력하거나 생성된 이름을 수락하거나 선호하는 **지역**을 선택한 후 **만들기 + 시작**을 클릭합니다.

3. 함수 앱에서 **함수 앱 설정** > **앱 서비스 설정으로 이동**을 클릭합니다.

	![함수 앱 설정 블레이드](./media/functions-create-an-event-processing-function/functions-app-service-settings.png)

4. 함수 앱에서 **모든 설정**을 클릭하고 **응용 프로그램 설정**으로 스크롤을 내린 다음, `sqldb_connection`**이름**에 대한 **연결 문자열**에서 연결 문자열을 **값**에 붙여넣습니다. **저장**을 클릭한 다음, 함수 앱 블레이드를 닫고 Functions 포털로 돌아옵니다.

    ![앱 서비스 설정 연결 문자열](./media/functions-create-an-event-processing-function/functions-app-service-settings-connection-strings.png)

이제 SQL 데이터베이스와 연결하는 C# 함수 코드를 추가할 수 있습니다.

## 템플릿에서 타이머로 트리거된 함수 만들기

1. 함수 앱에서 **+새 함수** > **TimerTrigger - C#** > **만들기**를 클릭합니다. 1분에 한 번씩 기본 일정에서 실행되는 기본 이름을 가진 함수를 만듭니다.

	![새 타이머로 트리거되는 함수 만들기](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)

2. **개발** 탭의 **코드** 창에서 기존 함수 코드의 맨 위에 다음 어셈블리 참조를 추가합니다.

		#r "System.Configuration"
		#r "System.Data"

3. 다음 `using` 문을 함수에 추가합니다.

		using System.Configuration;
		using System.Data.SqlClient;
		using System.Threading.Tasks; 

4. 기존 **Run** 함수를 다음 코드로 바꿉니다.

		public static async Task Run(TimerInfo myTimer, TraceWriter log)
		{
		    var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
		    using (SqlConnection conn = new SqlConnection(str))
		    {
		        conn.Open();
		        var text = "DELETE from dbo.TodoItems WHERE Complete='True'";
		        using (SqlCommand cmd = new SqlCommand(text, conn))
		        {
					// Execute the command and log the # rows deleted.
		            var rows = await cmd.ExecuteNonQueryAsync();
		            log.Info($"{rows} rows were deleted");
		        }
		    }
		}

5. **저장**을 클릭하고 **로그** 창에서 다음 함수 실행을 지켜본 다음, TodoItems 테이블에서 삭제된 행 개수를 메모합니다.

6. (선택 사항) [모바일 앱 퀵스타트 앱](../app-service-mobile/app-service-mobile-ios-get-started.md)을 사용하여 추가 항목을 "완료됨"으로 표시하고 **로그** 창으로 돌아와서 다음 함수 실행 시 같은 개수의 행이 삭제되는지 확인합니다.

##다음 단계

Azure Functions에 대한 자세한 내용은 다음 항목을 참조합니다.

+ [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
+ [Azure Functions 테스트](functions-test-a-function.md) 함수를 테스트하는 다양한 도구와 기법을 설명합니다.
+ [Azure Functions 크기 조정 방법](functions-scale.md) 동적 서비스 계획 등 Azure Functions에 사용할 수 있는 서비스 계획과 적절한 계획을 선택하는 방법을 설명합니다.

[AZURE.INCLUDE [시작 메모](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0928_2016-->