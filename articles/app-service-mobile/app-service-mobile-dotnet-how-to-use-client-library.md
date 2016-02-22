<properties
	pageTitle="앱 서비스 모바일 앱으로 관리되는 클라이언트 라이브러리 작업(Windows | Xamarin) | Microsoft Azure"
	description="Windows 및 Xamarin 앱에서 Azure 앱 서비스 모바일 앱용 .NET 클라이언트를 사용하는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Azure 모바일 앱에 관리되는 클라이언트를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##개요

이 가이드에서는 Windows 및 Xamarin 앱용 Azure 앱 서비스 모바일 앱에 관리되는 클라이언트 라이브러리를 사용하는 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 모바일 앱을 처음 접하는 경우 먼저 [모바일 앱 빠른 시작](app-service-mobile-windows-store-dotnet-get-started.md) 자습서를 완료하는 것이 좋습니다. 이 가이드에서는 클라이언트 쪽 관리되는 SDK에 초점을 둡니다. 모바일 앱용 서버 쪽 SDK에 대해 자세히 알아보려면 [.NET 백 엔드 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 또는 [Node.js 백 엔드 SDK를 사용하는 방법](app-service-mobile-node-backend-how-to-use-server-sdk.md)을 참조하세요.

## 참조 설명서

클라이언트 SDK에 대한 참조 설명서는 [Azure 모바일 앱 .NET 클라이언트 참조](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx)에서 찾을 수 있습니다.

##<a name="setup"></a>설정 및 필수 조건

하나의 테이블에 포함된 모바일 앱 백 엔드 프로젝트를 이미 만들고 게시했다고 가정합니다. 이 항목에 사용되는 코드에서 테이블은 이름이 `TodoItem`(이)고 `Id`, `Text` 및 `Complete` 열이 있습니다. [빠른 시작 자습서](app-service-mobile-windows-store-dotnet-get-started.md)를 완료할 때 생성되는 동일한 테이블입니다.

C#에서 해당하는 형식화된 클라이언트 쪽 형식은 다음과 같습니다.


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

[JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm)는 클라이언트 형식과 테이블 간의 *PropertyName* 매핑을 정의하는 데 사용됩니다.

모바일 앱 백 엔드에 새 테이블을 작성하는 방법을 알아보려면 [방법: 테이블 컨트롤러 정의](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller)(.NET 백 엔드) 또는 [동적 스키마를 사용하여 테이블 정의](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)(Node.js 백 엔드)를 참조하세요. 또한 Node.js 백 엔드의 경우 [Azure 포털](https://portal.azure.com/)에서 **쉬운 테이블** 설정을 사용할 수 있습니다.

##<a name="create-client"></a>방법: 모바일 앱 클라이언트 만들기

다음 코드는 모바일 앱 백 엔드에 액세스하는 데 사용되는 `MobileServiceClient` 개체를 만듭니다.

	MobileServiceClient client = new MobileServiceClient(
		"MOBILE_APP_URL");

위의 코드에서 `MOBILE_APP_URL`을 모바일 앱 백 엔드의 URL로 대체하며 이는 [Azure 포털](https://portal.azure.com/)의 모바일 앱 백 엔드에 대한 블레이드에서 찾을 수 있습니다.

##<a name="instantiating"></a>방법: 테이블 참조 만들기

백 엔드 테이블의 데이터에 액세스하거나 데이터를 수정하는 모든 코드는 `MobileServiceTable` 개체의 함수를 호출합니다. 다음과 같이 `MobileServiceClient`의 인스턴스에 [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) 메서드를 호출하여 테이블에 대한 참조를 얻습니다.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

형식화된 직렬화 모델입니다. 형식화되지 않은 직렬화 모델도 지원됩니다. 다음은 형식화되지 않은 테이블에 참조를 만듭니다.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

형식화되지 않은 쿼리에서 기본 OData 쿼리 문자열을 지정해야 합니다.

##<a name="querying"></a>방법: 모바일 앱에서 데이터 쿼리

이 섹션에서는 다음 기능을 비롯하여 모바일 앱 백 엔드에 대한 쿼리를 실행하는 방법을 설명합니다.

- [반환된 데이터 필터링]
- [반환된 데이터 정렬]
- [페이지에서 데이터 반환]
- [특정 열 선택]
- [ID를 기준으로 데이터 조회]

>[AZURE.NOTE] 모든 행이 반환되는 것을 방지하기 위해 서버 기반 페이지 크기가 적용됩니다. 그러면 대규모 데이터 집합에 대한 기본 요청이 서비스에 부정적인 영향을 미치지 않습니다. 50개가 넘는 행을 반환하려면 [페이지에서 데이터 반환]에서 설명하는 대로 `Take` 메서드를 사용하세요.

### <a name="filtering"></a>방법: 반환된 데이터 필터링

다음 코드는 쿼리에 `Where` 절을 포함하여 데이터를 필터링하는 방법을 보여줍니다. 이 코드에서는 `todoTable`에서 해당 `Complete` 속성이 `false`인 모든 항목을 반환합니다. `Where` 함수는 테이블에 대한 쿼리에 행 필터링 조건자를 적용합니다.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

브라우저 개발자 도구 또는 [Fiddler]와 같은 메시지 검사 소프트웨어를 사용하여 백 엔드에 전송된 요청의 URI를 볼 수 있습니다. 다음 요청 URI에서 알 수 있듯이 쿼리 문자열 자체를 수정합니다.

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

이 요청은 일반적으로 다음과 같은 Azure 쪽 SQL 쿼리로 변환됩니다.

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

`Where` 메서드에 전달되는 함수는 임의의 수의 조건을 가질 수 있습니다. 다음 줄을 예로 들겠습니다.

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

이 줄은 다음으로 변환됩니다(앞에서 나온 것과 동일한 요청에 대해).

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

위의 `where` 문은 null이 아닌 `Text`(으)로 `Complete` 상태가 false로 설정된 항목을 찾습니다.

여러 줄로 작성할 수도 있습니다.

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

두 메서드는 동등하며 서로 교환해서 사용할 수 있습니다. 여러 조건자를 하나의 쿼리에서 연결하는 첫 번째 옵션이 더 간편하며 권장됩니다.

`where` 절은 OData 하위 집합으로 변환되는 작업을 지원합니다. 여기에는 관계 연산자(==, !=, <, <=, >, >=), 산술 연산자(+, -, /, *, %), 숫자 정밀도(Math.Floor, Math.Ceiling), 문자열 함수(Length, Substring, Replace, IndexOf, StartsWith, EndsWith), 날짜 속성(Year, Month, Day, Hour, Minute, Second), 개체의 액세스 속성, 이 모든 항목을 결합하는 식이 포함됩니다.

### <a name="sorting"></a>방법: 반환된 데이터 정렬

다음 코드는 쿼리에 `OrderBy` 또는 `OrderByDescending` 함수를 포함하여 데이터를 정렬하는 방법을 보여 줍니다. `todoTable`의 항목을 `Text` 필드를 기준으로 오름차순 정렬한 항목을 반환합니다.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>방법: 페이지에서 데이터 반환

기본적으로 백 엔드는 첫 50개 행만 반환합니다. [Take] 메서드를 호출하여 반환 행 수를 늘릴 수 있습니다. `Take`을(를) [Skip] 메서드와 함께 사용하면 쿼리에서 반환되는 전체 데이터 집합의 특정 "페이지"가 요청됩니다. 다음 쿼리를 실행하면 테이블에서 맨 위에 있는 세 개의 항목을 반환합니다.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

수정된 다음 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

[IncludeTotalCount] 메서드를 사용하여 쿼리가 지정된 take 페이징/제한 절을 무시하고 반환되었을 <i>모든</i> 레코드의 총 개수를 가져오도록 할 수도 있습니다.

	query = query.IncludeTotalCount();

이것은 하드 코드된 페이징 값을 `Take` 및 `Skip` 메서드에 전달하는 간소화된 시나리오입니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다.

>[AZURE.NOTE]모바일 앱 백 엔드에서 50행 제한을 재정의하려면 [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx)를 공용 가져오기 메서드에도 적용하고 페이징 동작을 지정해야 합니다. 메서드에 적용할 때 다음은 최대 반환 행을 1000으로 설정합니다.

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>방법: 특정 열 선택

쿼리에 `Select` 절을 추가하면 결과에 포함할 속성 집합을 지정할 수 있습니다. 예를 들어 다음 코드는 하나의 필드만 선택하는 방법 및 여러 필드를 선택하고 형식을 지정하는 방법을 보여 줍니다.

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

지금까지 설명한 모든 함수는 가산적이므로 계속해서 호출할 수 있으며, 호출할 때마다 쿼리에 더 많은 영향이 있습니다. 한 가지 예를 더 들면 다음과 같습니다.

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>방법: ID를 기준으로 데이터 조회

`LookupAsync` 함수를 사용하여 데이터베이스에서 특정 ID를 가진 개체를 조회할 수 있습니다.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>방법: 형식화되지 않은 쿼리 실행

형식화되지 않은 테이블 개체를 사용하여 쿼리를 실행하는 경우 다음 예제와 같이 OData 쿼리 문자열을 명시적으로 지정해야 합니다.

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

속성 모음처럼 사용할 수 있는 JSON 값이 반환됩니다. JToken 및 JSON.NET에 대한 자세한 내용은 [JSON.NET](http://json.codeplex.com/)(영문)을 참조하십시오.

##<a name="inserting"></a>방법: 모바일 앱 백 엔드에 데이터 삽입

모든 클라이언트 형식은 **ID**라는 멤버를 포함해야 하며 이는 기본적으로 문자열입니다. 이 **ID**는 CRUD 작업을 오프라인에서 수행하는 데 필요합니다. 다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 삽입할 데이터가 포함되어 있습니다.

	await todoTable.InsertAsync(todoItem);

`todoTable.InsertAsync` 호출에 전달되는 `todoItem`에 고유한 사용자 지정 ID 값이 포함되지 않은 경우, ID 값이 서버에 의해 생성되며 클라이언트에 반환되는 `todoItem` 개체에 설정된 서버에서 설정됩니다.

형식화되지 않은 데이터를 삽입하려면 아래와 같이 JSON.NET을 이용할 수 있습니다.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

다음은 메일 주소를 고유 문자열 id로 사용하는 예제입니다.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###ID 값으로 작업

모바일 앱은 테이블의 **id** 열에 대한 고유한 사용자 지정 문자열 값을 지원합니다. 이를 통해 응용 프로그램에서 메일 주소 또는 사용자 이름과 같은 사용자 지정 값을 ID에 사용할 수 있습니다.

문자열 ID는 다음과 같은 이점을 제공합니다.

+ ID는 데이터베이스에 대한 왕복 없이도 생성됩니다.
+ 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
+ 응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

문자열 ID 값이 삽입된 레코드에 설정되지 않은 경우 모바일 앱 백 엔드는 해당 ID에 대한 고유한 값을 생성합니다. `Guid.NewGuid()` 메서드를 사용하여 클라이언트 또는 백 엔드에서 고유한 ID 값을 생성할 수 있습니다.

##<a name="modifying"></a>방법: 모바일 앱 백 엔드의 데이터 수정

다음 코드는 ID가 같은 기존 인스턴스를 새 정보로 업데이트하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 업데이트할 데이터가 포함되어 있습니다.

	await todoTable.UpdateAsync(todoItem);

형식화되지 않은 데이터를 삽입하려면 다음과 같이 Json.NET을 이용할 수 있습니다.
	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

업데이트할 때 ID를 지정해야 합니다. 백 엔드가 업데이트할 인스턴스를 식별하는 방법입니다. ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다. "ID" 값을 제공하지 않고 항목을 업데이트하려고 할 때 `ArgumentException`이 발생합니다.


##<a name="deleting"></a>방법: 모바일 앱 백 엔드의 데이터 삭제

다음 코드는 기존 인스턴스를 삭제하는 방법을 보여 줍니다. 인스턴스는 `todoItem`에 설정된 "Id" 필드로 식별됩니다.

	await todoTable.DeleteAsync(todoItem);

형식화되지 않은 데이터를 삭제하려면 다음과 같이 Json.NET을 이용할 수 있습니다.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

삭제 요청을 할 때 ID를 지정해야 합니다. 다른 속성은 서비스에 전달되지 않거나 서비스에서 무시됩니다. `DeleteAsync` 호출의 결과는 일반적으로 `null`입니다. 전달할 ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다. "ID" 필드를 설정하지 않고 항목을 삭제하려고 할 때 `MobileServiceInvalidOperationException`가 백 엔드에서 반환됩니다.

##<a name="#custom-api"></a>방법: 사용자 지정 API 호출

사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

사용자 지정 API를 호출하려면 클라이언트에서 [InvokeApiAsync] 메서드 오버로드 중 하나를 호출합니다. 예를 들어 다음 코드 줄은 백 엔드에서 **completeAll** API로 POST 요청을 보냅니다.

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

이 형식화된 메서드 호출을 실행하려면 **MarkAllResult** 반환 형식을 정의해야 합니다. 형식화된 메서드와 형식화되지 않은 메서드가 모두 지원됩니다. 이는 형식화되어 있고 페이로드를 보내지 않으며 쿼리 매개 변수가 없고 요청 헤더를 변경하지 않으므로 대체로 단순한 예제입니다. [InvokeApiAsync]의 보다 실질적인 예와 자세한 설명을 확인하려면 [Azure 모바일 서비스 클라이언트 SDK의 사용자 지정 API]를 참조하십시오.

##방법: 푸시 알림 등록

모바일 앱 클라이언트를 사용하면 Azure 알림 허브로 푸시 알림을 등록할 수 있습니다. 등록할 때 플랫폼 특정 푸시 알림 서비스(PNS)에서 구하는 핸들을 가져옵니다. 그런 다음 등록을 만들 때 태그와 함께 이 값을 제공합니다. 다음 코드는 Windows 알림 서비스(WNS)를 통한 푸시 알림에 Windows 앱을 등록합니다.

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

이 예제에서는 두 태그가 등록에 포함됩니다. 템플릿 등록 방법을 비롯하여 Windows 앱에 대한 자세한 내용은 [앱에 푸시 알림 추가](app-service-mobile-windows-store-dotnet-get-started-push.md)를 참조하세요.

Xamarin 앱에는 iOS 또는 Android 앱을 실행하는 앱을 각각 APNS(Apple Push Notification Service) 및 GCM(Google Cloud Messaging) 서비스에 등록하기 위해 몇 가지 추가적인 코드가 필요합니다. 자세한 내용은 **앱에 푸시 알림 추가** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push))를 참조하세요.

## 방법: 플랫폼 간 알림을 보내기 위해 푸시 템플릿 등록

템플릿을 등록하려면 클라이언트 앱에서 **MobileService.GetPush(). RegisterAsync()** 메서드를 사용하여 템플릿을 전달하기만 하면 됩니다.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

템플릿은 JObject 형식이며 다음 JSON 형식으로 여러 템플릿을 포함할 수 있습니다.

        public JObject newTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

또한 **RegisterAsync()** 메서드는 보조 타일을 허용할 수 있습니다.

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

보안에 대한 모든 태그는 제거될 것입니다. 설치에 태그를 추가하거나 설치 내에 템플릿을 추가하려면 [Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업]을 참조하세요.

이러한 등록된 템플릿을 활용하여 알림을 보내려면 [알림 허브 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)로 작업합니다.

##<a name="optimisticconcurrency"></a>방법: 낙관적 동시성 사용

일부 시나리오에서 두 개 이상의 클라이언트가 동시에 동일 항목의 변경 내용을 작성할 수 있습니다. 충돌 검색 없이, 마지막으로 쓴 내용이 원하는 결과가 아닌 경우에도 이전 업데이트를 덮어씁니다. *낙관적 동시성 제어*에서는 각 트랜잭션이 커밋할 수 있으므로 리소스 잠금을 사용하지 않는다고 가정합니다. 트랜잭션을 커밋하기 전에 낙관적 동시성 제어는 다른 트랜잭션에서 데이터를 수정하지 않았음을 확인합니다. 데이터가 수정된 경우에는 커밋 중인 트랜잭션이 롤백됩니다.

모바일 앱은 모바일 앱 백 엔드의 각 테이블에 대해 정의된 `__version` 시스템 속성 열을 사용하는 각 항목의 변경 내용을 추적하여 낙관적 동시성 제어를 지원합니다. 레코드가 업데이트될 때마다 모바일 앱은 해당 레코드의 `__version` 속성을 새 값으로 설정합니다. 각 업데이트 요청 중에 요청에 포함된 레코드의 `__version` 속성이 서버에 있는 레코드의 동일 속성과 비교됩니다. 요청과 함께 전달된 버전이 백 엔드와 일치하지 않는 경우 클라이언트 라이브러리는 `MobileServicePreconditionFailedException<T>`을 발생시킵니다. 예외에 포함된 형식은 백 엔드의 레코드이며 서버 버전의 레코드를 포함하고 있습니다. 그러면 응용 프로그램은 이 정보를 사용하여 변경을 커밋하기 위해 백 엔드의 올바른 `__version` 값으로 업데이트 요청을 다시 실행할지 여부를 결정할 수 있습니다.

낙관적 동시성을 사용하기 위해 응용 프로그램은 `__version` 시스템 속성의 테이블 클래스에 대해 열을 정의합니다. 다음 정의는 예제를 제공합니다.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


형식화되지 않은 테이블을 사용하는 응용 프로그램은 다음과 같이 테이블의 `SystemProperties`에 대해 `Version` 플래그를 설정하여 낙관적 동시성을 사용합니다.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


다음 코드는 감지된 쓰기 충돌을 해결하는 방법을 보여 줍니다. 해결 방법이 적용되려면 올바른 `__version` 값을 `UpdateAsync()` 호출에 포함해야 합니다.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화](app-service-mobile-offline-data-sync.md)를 참조하세요.


##<a name="binding"></a>방법: 모바일 앱 데이터를 Windows 사용자 인터페이스에 바인딩

이 섹션에서는 반환된 데이터 개체를 Windows 앱의 UI 요소를 사용해서 표시하는 방법을 보여 줍니다. `todoTable`에서 완료되지 않은 항목을 쿼리하고 이를 매우 간단한 목록으로 표시하려면 다음 예제 코드를 실행하여 목록 원본을 쿼리와 바인딩하면 됩니다. `MobileServiceCollection`을 사용하면 모바일 앱 인식 바인딩 컬렉션이 만들어집니다.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

관리되는 런타임의 일부 컨트롤은 [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916)이라는 인터페이스를 지원합니다. 이 인터페이스에서는 사용자가 스크롤할 때 컨트롤이 추가 데이터를 요청할 수 있습니다. 컨트롤에서 발생하는 호출을 자동으로 처리하는 `MobileServiceIncrementalLoadingCollection`을 통해 이 유니버설 Windows 8.1 앱용 인터페이스를 기본적으로 지원합니다. Windows 앱에서 `MobileServiceIncrementalLoadingCollection`을 사용하려면 다음을 수행합니다.

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Windows Phone 8 및 "Silverlight" 앱에서 새 컬렉션을 사용하려면 `IMobileServiceTableQuery<T>` 및 `IMobileServiceTable<T>`에서 `ToCollection` 확장 메서드를 사용합니다. 실제로 데이터를 로드하려면 `LoadMoreItemsAsync()`을(를) 호출합니다.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

`ToCollectionAsync` 또는 `ToCollection`을(를) 호출하여 만들어진 컬렉션을 사용하는 경우 UI 컨트롤에 바인딩할 수 있는 컬렉션을 얻게 됩니다. 이 컬렉션은 페이징을 인식합니다. 다시 말해서, 컨트롤은 "더 많은 항목을 로드"하도록 컬렉션에 요청할 수 있고 컬렉션은 이를 수행합니다. 이때 사용자 코드가 사용되지 않으며 컨트롤에서 흐름을 시작합니다. 하지만 컬렉션이 네트워크에서 데이터를 로드하기 때문에 로딩에 실패하는 경우도 있습니다. 이 오류를 처리하려면 컨트롤에서 수행한 `LoadMoreItemsAsync` 호출의 결과로 발생한 예외를 처리하도록 `MobileServiceIncrementalLoadingCollection`에 대한 `OnException` 메서드를 재정의할 수 있습니다.

마지막으로, 테이블에 필드가 많지만 컨트롤에 일부 필드만 표시하려는 경우를 가정하겠습니다. 위에 나온 “[특정 열 선택](#selecting)” 섹션의 참고 자료에 따라 UI에 표시할 특정 열을 선택할 수 있습니다.

## <a name="adal"></a>방법: Active Directory 인증 라이브러리를 사용하여 사용자 인증

Azure Active Directory를 사용하여 응용 프로그램에 사용자가 로그인하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다. `loginAsync()` 메서드는 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기에 해당 메서드 사용을 선호할 수 있습니다.

1. 다음으로 [Active Directory 로그인에 앱 서비스를 구성하는 방법](app-service-mobile-how-to-configure-active-directory-authentication.md) 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 응용 프로그램을 등록하는 선택적 단계를 완료해야 합니다.

2. Visual Studio 또는 Xamarin Studio에서 프로젝트를 열고 `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet 패키지에 참조를 추가합니다. 검색할 때 시험판 버전을 포함합니다.

3. 사용하는 플랫폼에 따라 응용 프로그램에 아래 코드를 추가합니다. 각각에서 다음과 같이 대체합니다.

* **INSERT-AUTHORITY-HERE**를 응용 프로그램이 프로비전된 테넌트의 이름으로 바꿉니다. https://login.windows.net/contoso.onmicrosoft.com 형식이어야 합니다. 이 값은 [Azure 클래식 포털]의 Azure Active Directory에 있는 도메인 탭에서 복사될 수 있습니다.

* **INSERT-RESOURCE-ID-HERE**를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 **Azure Active Directory 설정**에 있는 **고급**에서 이를 가져올 수 있습니다.

* **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

* HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE**를 사이트의 _/.auth/login/done_ 끝점으로 바꿉니다. 이 값은 \__https://contoso.azurewebsites.net/.auth/login/done_와 유사해야 합니다.

각 플랫폼에 필요한 코드는 다음과 같습니다.

**Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

**Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
			}
        }

**Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.SetMessage(ex.Message);
				builder.SetTitle("You must log in. Login Required");
				builder.Create().Show();
			}
        }
		protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
		{
			base.OnActivityResult(requestCode, resultCode, data);
			AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
		}

## <a name="package-sid"></a>방법: Windows 스토어 패키지 SID 가져오기

Windows 앱의 경우 푸시 알림 및 특정 인증 모드를 사용할 수 있도록 하려면 패키지 SID가 필요합니다. 이 값을 가져오려면

1. Visual Studio 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어** > **스토어와 앱을 연결...**을 클릭합니다.
2. 마법사에서 **다음**을 클릭하고 Microsoft 계정으로 로그인하고 **새로운 앱 이름 예약**에서 앱 이름을 입력한 후 **예약**을 클릭합니다.
3. 앱을 성공적으로 등록한 후에 새로운 앱 이름을 선택하고 **다음** 및 **연결**을 차례로 클릭합니다. 이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.
4. Microsoft 계정을 사용하여 [Windows 개발자 센터](https://dev.windows.com/ko-KR/overview)에 로그인합니다. **내 앱**에서 방금 만든 앱 등록을 클릭합니다.
5. **앱 관리** > **앱 ID**를 클릭한 다음 아래로 스크롤하여 **패키지 SID**를 찾습니다.

패키지 SID를 URI로 처리하여 다양하게 사용할 수 있으며, 이 경우에 _ms-app://_를 스키마로 사용해야 합니다. 이 값을 접두사로 연결하여 만든 패키지 SID의 버전을 기록합니다.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](mobile-services-how-to-register-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>방법: 오류 처리

다음 예제에서는 백 엔드에서 반환되는 예외를 처리하는 방법을 보여줍니다.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}


##<a name="unit-testing"></a>방법: 설계 단위 테스트

`MobileServiceClient.GetTable` 및 쿼리에서 반환되는 값은 인터페이스입니다. 이 값을 사용하면 테스트 목적으로 쉽게 인터페이스를 "모방"할 수 있으므로, 테스트 논리를 구현하는 `MyMockTable : IMobileServiceTable<TodoItem>`을(를) 만들 수 있습니다.

##<a name="customizing"></a>방법: 클라이언트 사용자 지정

이 섹션에서는 요청 헤더를 사용자 지정하고 응답에서 JSON 개체의 serialization을 사용자 지정할 수 있는 방법을 보여줍니다.

### <a name="headers"></a>방법: 요청 헤더 사용자 지정

특정 앱 시나리오를 지원하려면 모바일 앱 백 엔드와의 통신을 사용자 지정해야 할 수 있습니다. 예를들어, 모든 보내는 요청이나 변경 응답 상태 코드에 사용자 지정 헤더를 추가하고자 할 수 있습니다. 다음 예제와 같이 사용자 지정 [DelegatingHandler]를 제공하여 이를 수행할 수 있습니다.

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

이 코드는 요청에 새 **x-my-header** 헤더를 추가하고 임의로 응답 코드를 사용할 수 없도록 설정합니다. 실제 시나리오에서는 앱에서 요청하는 일부 사용자 지정 논리에 기반하여 응답 상태 코드를 설정합니다.

### <a name="serialization"></a>방법: serialization 사용자 지정

모바일 앱 클라이언트 라이브러리는 Json.NET을 사용하여 클라이언트에서 .NET 개체로 JSON 응답을 변환합니다. 메시지의 .NET 형식 및 JSON 간에 이 serialization의 동작을 구성할 수 있습니다. [MobileServiceClient] 클래스는 [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)의 `SerializerSettings` 속성을 표시합니다.

이 속성을 사용하여 다음과 같은 다양한 Json.NET 속성 중 하나를 설정할 수 있습니다.

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

이 속성은 모든 속성을 직렬화 중에 소문자로 변환합니다.

<!-- Anchors. -->
[반환된 데이터 필터링]: #filtering
[반환된 데이터 정렬]: #sorting
[페이지에서 데이터 반환]: #paging
[특정 열 선택]: #selecting
[ID를 기준으로 데이터 조회]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Azure 모바일 서비스 클라이언트 SDK의 사용자 지정 API]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0211_2016-->