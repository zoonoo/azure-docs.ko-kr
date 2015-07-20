<properties 
	pageTitle="Xamarin Component 클라이언트를 사용하는 방법 - Azure 모바일 서비스 기능 가이드" 
	description="Azure 모바일 서비스에 Xamarin Component 클라이언트를 사용하는 방법에 대해 알아봅니다." 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="lindydonna"/>

# Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법
[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

이 가이드에서는 iOS 및 Android용 Xamarin 앱에서 Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하여 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 데이터 쿼리, 삽입, 업데이트, 삭제 및 사용자 인증과 오류 처리가 포함됩니다. 모바일 서비스를 처음 사용하는 경우 먼저 "모바일 서비스 빠른 시작" 자습서([Xamarin.iOS][Xamarin.iOS quickstart tutorial]/[Xamarin.Android][Xamarin.Android quickstart tutorial]) 및 ".NET에서 데이터 시작" 자습서([Xamarin.iOS][Xamarin.iOS data tutorial]/[Xamarin.Android][Xamarin.Android data tutorial])를 완료해야 합니다. 빠른 시작 자습서에는 [Xamarin][Xamarin download] 및 [모바일 서비스 SDK]가 필요하며, 이 자습서를 참조하여 계정을 구성하고 첫 모바일 서비스를 만들 수 있습니다.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="setup"></a>설정 및 필수 조건

이미 모바일 서비스 및 테이블을 만들었다고 가정합니다. 자세한 내용은 [테이블 만들기](http://go.microsoft.com/fwlink/?LinkId=298592)를 참조하십시오. 이 항목에 사용되는 코드에서 테이블은 이름이 `TodoItem`(이)고 `id`, `Text` 및 `Complete` 열이 있습니다.

해당 형식화된 클라이언트 쪽 .NET 유형은 다음과 같습니다.


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
동적 스키마가 사용하도록 설정된 경우 Azure 모바일 서비스에서 삽입 또는 업데이트 요청의 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://go.microsoft.com/fwlink/?LinkId=296271)를 참조하십시오.

## <a name="create-client"></a>방법: 모바일 서비스 클라이언트 만들기

다음 코드는 모바일 서비스에 액세스하는 데 사용되는 `MobileServiceClient` 개체를 만듭니다.
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

위 코드에서 `AppUrl` 및 `AppKey`을(를) 모바일 서비스 URL 및 응용 프로그램 키로 바꿉니다(두 항목 순서 유지). 이 두 항목은 모두 Azure 관리 포털에서 사용할 수 있습니다. 모바일 서비스를 선택한 후 "대시보드"를 클릭하면 됩니다.

## <a name="instantiating"></a>방법: 테이블 참조 만들기

모바일 서비스 테이블의 데이터에 액세스하거나 데이터를 수정하는 모든 코드는 `MobileServiceTable` 개체에 대한 함수를 호출합니다. `MobileServiceClient` 인스턴스에 대해 [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) 함수를 호출하여 테이블에 대한 참조를 구합니다.

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

형식화된 serialization 모델입니다. 아래의 <a href="#untyped">형식화되지 않은 serialization 모델</a>을 참조하십시오.
			
## <a name="querying"></a>방법: 모바일 서비스에서 데이터 쿼리 

이 섹션에서는 모바일 서비스에 대한 쿼리를 실행하는 방법을 설명합니다. 하위 섹션에서는 정렬, 필터링, 페이징과 같은 다양한 측면에 대해 설명합니다.
			
### <a name="filtering"></a>방법: 반환된 데이터 필터링

다음 코드는 쿼리에 `Where` 절을 포함하여 데이터를 필터링하는 방법을 보여줍니다. 이 코드에서는 `todoTable`에서 해당 `Complete` 속성이 `false`인 모든 항목을 반환합니다. `Where` 함수는 테이블에 대한 쿼리에 행 필터링 조건자를 적용합니다.
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

브라우저 개발 도구 또는 Fiddler와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 볼 수 있습니다. 다음 요청 URI에서 알 수 있듯이 쿼리 문자열 자체를 수정합니다.

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
이 요청은 일반적으로 다음과 같은 서버 쪽 SQL 쿼리로 변환됩니다.
			
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

`where` 절은 모바일 서비스 OData 하위 집합으로 변환되는 연산을 지원합니다. 여기에는 관계 연산자(==, !=, <, <=, >, >=), 산술 연산자(+, -, /, *, %), 숫자 정밀도(Math.Floor, Math.Ceiling), 문자열 함수(Length, Substring, Replace, IndexOf, StartsWith, EndsWith), 날짜 속성(Year, Month, Day, Hour, Minute, Second), 개체의 액세스 속성, 이 모든 항목을 결합하는 식이 포함됩니다.

### <a name="sorting"></a>방법: 반환된 데이터 정렬

다음 코드는 쿼리에 `OrderBy` 또는 `OrderByDescending` 함수를 포함하여 데이터를 정렬하는 방법을 보여 줍니다. `todoTable`의 항목을 `Text` 필드를 기준으로 오름차순 정렬한 항목을 반환합니다. 기본적으로 서버는 첫 50개 요소만 반환합니다.

> [AZURE.NOTE]모든 요소가 반환되는 것을 방지하기 위해 기본적으로 서버 기반 페이지 크기가 사용됩니다. 그러면 대규모 데이터 집합에 대한 기본 요청이 서비스에 부정적인 영향을 미치지 않습니다.

다음 섹션에 설명된 대로 `Take`을(를) 호출하여 반환할 항목 수를 늘릴 수 있습니다.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>방법: 페이지에서 데이터 반환

다음 코드는 쿼리에 `Take` 및 `Skip` 절을 사용하여 반환된 데이터의 페이징을 구현하는 방법을 보여줍니다. 다음 쿼리를 실행하면 테이블에서 맨 위에 있는 세 개의 항목을 반환합니다.

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
			
[IncludeTotalCount](http://msdn.microsoft.com/library/windowsazure/jj730933.aspx) 메서드를 사용하여 쿼리가 지정된 take 페이징/제한 절을 무시하고 반환되었을 <i>모든</i> 레코드의 총 개수를 가져오도록 할 수도 있습니다.

	query = query.IncludeTotalCount();

이것은 하드 코드된 페이징 값을 `Take` 및 `Skip` 메서드에 전달하는 간소화된 시나리오입니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다.

### <a name="selecting"></a>방법: 특정 열 선택

쿼리에 `Select` 절을 추가하면 결과에 포함할 속성 집합을 지정할 수 있습니다. 예를 들어 다음 코드는 하나의 필드만 선택하는 방법 및 여러 필드를 선택하고 형식을 지정하는 방법을 보여 줍니다.

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
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

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

## <a name="inserting"></a>방법: 모바일 서비스에 데이터 삽입

> [AZURE.NOTE]형식에 대해 삽입, 조회, 삭제 또는 업데이트 작업을 수행하려는 경우 **Id**(대/소문자 구분 안 함)라는 멤버를 만들어야 합니다. 이 때문에 예제 클래스 **TodoItem**에 이름이 **Id**인 멤버가 있습니다. 삽입 작업 중에는 ID 값을 기본값이 아닌 값으로 설정하면 안 됩니다. 반면, 업데이트 및 삭제 작업에서는 ID 값이 항상 있고 기본값이 아닌 값으로 설정되어야 합니다.

다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 삽입할 데이터가 포함되어 있습니다.

	await todoTable.InsertAsync(todoItem);

await `todoTable.InsertAsync` 호출이 반환된 후 서버의 개체 ID가 클라이언트의 `todoItem` 개체에 채워집니다.

형식화되지 않은 데이터를 삽입하려면 아래와 같이 JSON.NET을 이용할 수 있습니다. 개체를 삽입할 때는 ID를 지정하면 안 됩니다.

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

"Id" 필드가 이미 설정된 항목을 삽입하려고 하면 서비스에서 `MobileServiceInvalidOperationException`이(가) 반환됩니다.

## <a name="modifying"></a>방법: 모바일 서비스의 데이터 수정

다음 코드는 ID가 같은 기존 인스턴스를 새 정보로 업데이트하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 업데이트할 데이터가 포함되어 있습니다.

	await todoTable.UpdateAsync(todoItem);


형식화되지 않은 데이터를 삽입하려면 다음과 같이 JSON.NET을 이용할 수 있습니다. 업데이트할 때는 ID를 지정해야 합니다. 모바일 서비스가 업데이트할 인스턴스를 식별하는 방법이 ID이기 때문입니다. ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
"Id" 필드가 설정되지 않은 항목을 업데이트하려고 하면 서비스가 업데이트할 인스턴스를 구분할 수 없으므로 서비스에서 `MobileServiceInvalidOperationException`이(가) 반환됩니다. 마찬가지로, "Id" 필드가 설정되지 않은 형식화되지 않은 항목을 업데이트하려는 경우에도 서비스에서 `MobileServiceInvalidOperationException`이(가) 반환됩니다.
			
			
## <a name="deleting"></a>방법: 모바일 서비스의 데이터 삭제

다음 코드는 기존 인스턴스를 삭제하는 방법을 보여 줍니다. 인스턴스는 `todoItem`에 설정된 "Id" 필드로 식별됩니다.

	await todoTable.DeleteAsync(todoItem);

형식화되지 않은 데이터를 삭제하려면 다음과 같이 JSON.NET을 이용할 수 있습니다. 삭제 요청을 할 때는 ID를 지정해야 합니다. 모바일 서비스가 삭제할 인스턴스를 식별하는 방법이 ID이기 때문입니다. 삭제 요청에는 ID만 필요하며, 다른 속성은 서비스에 전달되지 않습니다. 다른 속성이 전달되는 경우에도 서비스에서 무시됩니다. `DeleteAsync` 호출의 결과도 일반적으로 `null`입니다. 전달할 ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
"Id" 필드가 설정되지 않은 항목을 삭제하려고 하면 서비스가 삭제할 인스턴스를 구분할 수 없으므로 서비스에서 `MobileServiceInvalidOperationException`이(가) 반환됩니다. 마찬가지로, "Id" 필드가 설정되지 않은 형식화되지 않은 항목을 삭제하려는 경우에도 서비스에서 `MobileServiceInvalidOperationException`이(가) 반환됩니다.
		


## <a name="authentication"></a>방법: 사용자 인증

모바일 서비스는 Facebook, Google, Microsoft 계정, Twitter 및 Azure Active Directory와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 "인증 시작" 자습서([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication])를 참조하십시오.

두 가지의 인증 흐름, 즉 _서버 흐름_과 _클라이언트 흐름_이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하므로 장치 특정 기능을 통해 더욱 강력한 통합이 가능합니다.

### 서버 흐름
모바일 서비스가 Windows 스토어 또는 Windows Phone 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, 모바일 서비스에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다. 자세한 내용은 "인증 시작" 자습서([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication])를 참조하십시오.

ID 공급자를 등록하고 나면 공급자의 [MobileServiceAuthenticationProvider] 값을 사용하여 [LoginAsync 메서드]를 호출합니다. 예를 들어 다음 코드는 Facebook을 사용한 서버 흐름 로그인을 시작합니다.

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

Facebook 이외의 ID 공급자를 사용하는 경우 위의 [MobileServiceAuthenticationProvider] 값을 공급자에 대한 값으로 변경합니다.

이 경우 모바일 서비스는 선택한 공급자의 로그인 페이지를 표시하고 ID 공급자 로그인 후 모바일 서비스 인증 토큰을 생성하여 OAuth 2.0 인증 흐름을 관리합니다. [LoginAsync 메서드]는 [MobileServiceUser]를 반환하며, 여기서 인증된 사용자의 [userId] 및 [MobileServiceAuthenticationToken]이 JWT(JSON 웹 토큰)로 제공됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다. 자세한 내용은 [인증 토큰 캐시]를 참조하십시오.

### 클라이언트 흐름

앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 인증을 위해 모바일 서비스에 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그인 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다.

가장 간소화된 형태로, 다음과 같은 Facebook 또는 Google용 코드 조각에 나온 대로 클라이언트 흐름을 사용할 수 있습니다.

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

### <a name="caching"></a>인증 토큰 캐시
일부 경우, 사용자가 처음으로 인증된 후에 login 메서드 호출을 방지할 수 있습니다. 로컬 보안 저장소(예: [Xamarin.Auth][Xamarin.Auth component])를 사용하여 처음 로그인할 때 현재 사용자 ID를 캐시하고 이후에 매번 캐시에서 해당 사용자 ID가 이미 있는지 확인할 수 있습니다. 캐시가 비어 있는 경우에도 사용자가 로그인 프로세스를 거치도록 해야 합니다.

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
	accountStore.Delete(account, "Facebook");


## <a name="errors"></a>방법: 오류 처리

모바일 서비스에서 오류가 발생하고, 오류의 유효성을 검사하고, 오류를 해결하는 방법이 몇 가지 있습니다.

예를 들어 서버 스크립트를 모바일 서비스에 등록하여 삽입 및 업데이트할 데이터에 대해 유효성 확인 및 데이터 수정 등 다양한 작업을 수행하는 데 사용할 수 있습니다. 데이터 유효성 검사 및 수정을 수행하는 서버 스크립트를 다음과 같이 정의하고 등록합니다.

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

이 서버 쪽 스크립트는 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부합니다.

모바일 서비스가 서버 쪽에서 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 .NET 앱을 업데이트할 수 있습니다.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
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

## <a name="untyped"></a>방법: 형식화되지 않은 데이터 작업

Xamarin Component 클라이언트는 강력한 형식의 시나리오에 맞게 설계되었습니다. 하지만 예를 들어 개방형 스키마로 개체를 처리하는 경우처럼 보다 느슨한 형식의 환경이 편리한 경우도 있습니다. 이 시나리오는 다음과 같이 실행됩니다. 쿼리에서 LINQ를 사용하지 않고 통신 형식을 사용합니다.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

속성 모음처럼 사용할 수 있는 JSON 값이 반환됩니다. JToken 및 JSON.NET에 대한 자세한 내용은 [JSON.NET](http://json.codeplex.com/)(영문)을 참조하십시오.

## <a name="unit-testing"></a>방법: 설계 단위 테스트

`MobileServiceClient.GetTable` 및 쿼리에서 반환되는 값은 인터페이스입니다. 이 값을 사용하면 테스트 목적으로 쉽게 인터페이스를 "모방"할 수 있으므로, 테스트 논리를 구현하는 `MyMockTable : IMobileServiceTable<TodoItem>`을(를) 만들 수 있습니다.

## <a name="nextsteps"></a>다음 단계

이 방법 개념 참조 항목을 마쳤습니다. 이제 모바일 서비스에서 중요한 작업을 수행하는 방법을 자세히 알아보겠습니다.

* 모바일 서비스 시작([Xamarin.iOS][Get started with Mobile Services iOS]/[Xamarin.Android][Get started with Mobile Services Android]) <br/>모바일 서비스 사용 방법에 대한 기본 사항을 알아봅니다.

* 데이터 시작([Xamarin.iOS][Get started with data iOS]/[Xamarin.Android][Get started with data Android]) <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* 인증 시작([Xamarin.iOS][Get started with authentication iOS]/[Xamarin.Android][Get started with authentication Android]) <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* 스크립트를 사용하여 데이터 유효성 검사 및 수정([Xamarin.iOS][Validate and modify data with scripts ios]/[Xamarin.Android][Validate and modify data with scripts android]) <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

* 페이징을 사용하여 쿼리 구체화([Xamarin.iOS][Refine queries with paging iOS]/[Xamarin.Android][Refine queries with paging Android]) <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

* 스크립트를 통해 사용자 권한 부여([Xamarin.iOS][Authorize users with scripts iOS]/[Xamarin.Android][Authorize users with scripts Android]) <br/>인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 방법을 알아봅니다.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[인증 토큰 캐시]: #caching

<!-- URLs. -->
[Get started with Mobile Services iOS]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with Mobile Services Android]: /develop/mobile/tutorials/get-started-xamarin-android
[Xamarin download]: http://xamarin.com/download/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.iOS quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-ios/
[Xamarin.Android quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-android/
[Xamarin.iOS data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Xamarin.Android data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Xamarin.iOS authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Xamarin.Android authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.Auth component]: https://components.xamarin.com/view/xamarin.auth

[모바일 서비스 SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Get started with data iOS]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with data Android]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication iOS]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with authentication Android]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validate and modify data with scripts ios]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validate and modify data with scripts android]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refine queries with paging iOS]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Refine queries with paging Android]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Authorize users with scripts iOS]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Authorize users with scripts Android]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[LoginAsync 메서드]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[LoginAsync 메서드]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
 

<!---HONumber=July15_HO2-->