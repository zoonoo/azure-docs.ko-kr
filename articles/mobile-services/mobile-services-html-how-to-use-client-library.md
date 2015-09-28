<properties 
	pageTitle="HTML 클라이언트를 사용하는 방법 | Microsoft Azure" 
	description="Azure 모바일 서비스에 HTML 클라이언트를 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="glenga"/>

# Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##개요

이 가이드에서는 Windows Store JavaScript 및 PhoneGap/CordovaAzure 앱을 포함하는 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하여 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 데이터 쿼리, 삽입, 업데이트, 삭제 및 사용자 인증과 오류 처리가 포함됩니다. 모바일 서비스를 처음 접하는 경우 먼저 [모바일 서비스 빠른 시작](mobile-services-html-get-started.md)을 완료하는 것이 좋습니다. 빠른 시작 자습서를 참조하여 계정을 구성하고 첫 모바일 서비스를 만들 수 있습니다.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>방법: 모바일 서비스 클라이언트 만들기

모바일 서비스 클라이언트에 대한 참조를 추가하는 방법은 다음을 포함하는 앱 플랫폼에 따라 다릅니다.

- 웹 기반 응용 프로그램의 경우, HTML 파일을 열고 페이지의 스크립트 참조에 다음을 추가합니다.

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js"></script>

- JavaScript/HTML로 작성된 Windows 스토어 앱의 경우, 프로젝트에 **WindowsAzure.MobileServices.WinJS** NuGet 패키지를 추가합니다.

- PhoneGap 또는 Cordova 앱의 경우 프로젝트에 [모바일 서비스 플러그인](https://github.com/Azure/azure-mobile-services-cordova)을 추가합니다. 이 플러그인은 [푸시 알림](#push-notifications)을 지원합니다.

편집기에서 JavaScript 파일을 열거나 만들고 `MobileServiceClient` 변수를 정의하는 다음 코드를 추가하고 `MobileServiceClient` 생성자에 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 이 순서대로 제공합니다.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

자리 표시자 `AppUrl`을(를) 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키가 있는 `AppKey`(으)로 바꿔야 합니다. 모바일 서비스에 대한 응용 프로그램 URL 및 응용 프로그램 키를 가져오는 방법에 대해 알아보려면 [기존 앱에 모바일 서비스 추가](mobile-services-html-get-started-data.md) 자습서를 참조하세요.

>[AZURE.IMPORTANT]응용 프로그램 키는 모바일 서비스에 대한 임의 요청을 필터링하기 위한 것이며 응용 프로그램과 함께 배포됩니다. 이 키는 암호화되지 않으므로 안전하다고 볼 수 없습니다. 모바일 서비스 데이터를 안전하게 유지하려면 액세스를 허용하기 전에 사용자를 인증해야 합니다. 자세한 내용은 [방법: 사용자 인증](#authentication)을 참조하세요.

##<a name="querying"></a>방법: 모바일 서비스에서 데이터 쿼리

SQL Database 테이블의 데이터에 액세스하거나 데이터를 수정하는 모든 코드는 `MobileServiceTable` 개체에 대한 함수를 호출합니다. `MobileServiceClient` 인스턴스에 대해 `getTable()` 함수를 호출하여 테이블에 대한 참조를 구합니다.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>방법: 반환된 데이터 필터링

다음 코드는 쿼리에 `where` 절을 포함하여 데이터를 필터링하는 방법을 보여줍니다. `todoItemTable`에서 완료 필드가 `false`와(과) 동일한 모든 항목을 반환합니다.`todoItemTable`은(는) 이전에 만든 모바일 서비스 테이블에 대한 참조입니다. where 함수는 테이블에 대한 쿼리에 행 필터링 조건자를 적용합니다. JSON 개체나 행 필터를 정의하는 함수를 인수로 허용하며 추가로 작성할 수 있는 쿼리를 반환합니다.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Query 개체에 `where` 호출을 추가하고 매개 변수로 개체를 전달하여 모바일 서비스에서 `complete` 열에 `false` 값이 포함된 행만 반환하도록 지정합니다. 또한 다음 요청 URI에서 알 수 있듯이 쿼리 문자열 자체를 수정합니다.

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

브라우저 개발 도구 또는 Fiddler와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 볼 수 있습니다.

이 요청은 일반적으로 다음과 같은 서버 쪽 SQL 쿼리로 변환됩니다.

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

`where` 메서드에 전달되는 개체에는 임의의 수의 매개 변수가 있을 수 있으며, 이 매개 변수는 모두 쿼리에 대한 AND 절로 변환됩니다. 다음 줄을 예로 들겠습니다.

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

이 줄은 다음으로 변환됩니다(앞에서 나온 것과 동일한 요청에 대해).

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

위의 `where` 문과 SQL 쿼리는 "david"에게 할당되었으며 난이도가 "medium"인 완료되지 않은 항목을 찾습니다.

하지만 동일한 쿼리를 작성하는 다른 방법이 있습니다. Query 개체에 대한 `.where` 호출은 `WHERE` 절에 `AND` 식을 추가하므로, 다음과 같이 세 줄에 이 호출을 작성해도 됩니다.

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

또는 다음과 같이 흐름 API를 사용합니다.

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

두 메서드는 동등하며 서로 교환해서 사용할 수 있습니다. 지금까지 모든 `where` 호출은 일부 매개 변수가 있는 개체를 사용하며 데이터베이스의 데이터에 대해 같음 비교가 이루어집니다. 하지만 query 메서드에 대한 다른 오버로드도 있으며, 여기서는 개체 대신 함수가 사용됩니다. 이 함수에서는 같지 않음과 같은 연산자 및 기타 관계형 연산을 사용하여 더 복잡한 식을 작성할 수 있습니다. 이 함수에서는 `this` 키워드가 서버 개체에 바인딩됩니다.

함수 본문은 쿼리 문자열 매개 변수에 전달되는 OData(Open Data Protocol) 부울 식으로 변환됩니다. 매개 변수를 취하지 않는 함수에 전달할 수 있으며, 다음과 같습니다.

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


매개 변수가 있는 함수에 전달하는 경우, `where` 절 뒤의 인수는 함수 매개 변수에 순서대로 바인딩됩니다. 이 함수 범위 외부에서 가져온 개체는 반드시 매개 변수로 전달해야 합니다. 함수가 외부 변수를 캡처할 수 없기 때문입니다. 다음 두 개의 예제에서 인수 "david"은 매개 변수 `name`에 바인딩되며, 첫 번째 예제에서 인수 "medium"도 매개 변수 `level`에 바인딩됩니다. 또한 함수는 지원되는 식이 포함된 단일 `return` 식으로 구성되어야 합니다. 다음과 같습니다.

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

따라서 규칙을 따르는 한, 다음과 같이 더 복잡한 필터를 데이터베이스 쿼리에 추가할 수 있습니다.

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

`where`와(과) `orderBy`, `take` 및 `skip`을(를) 결합할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

### <a name="sorting"></a>방법: 반환된 데이터 정렬

다음 코드는 쿼리에 `orderBy` 또는 `orderByDescending` 함수를 포함하여 데이터를 정렬하는 방법을 보여 줍니다. `todoItemTable`의 항목을 `text` 필드를 기준으로 오름차순 정렬한 항목을 반환합니다. 기본적으로 서버는 첫 50개 요소만 반환합니다.

> [AZURE.NOTE]모든 요소가 반환되는 것을 방지하기 위해 기본적으로 서버 기반 페이지 크기가 사용됩니다. 그러면 대규모 데이터 집합에 대한 기본 요청이 서비스에 부정적인 영향을 미치지 않습니다. 다음 섹션의 설명대로 `take`을(를) 호출하면 호출할 항목 수를 늘릴 수 있습니다. `todoItemTable`은(는) 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>방법: 페이지에서 데이터 반환

기본적으로 모바일 서비스는 클라이언트가 응답에 더 많은 데이터를 명시적으로 요청한 경우가 아니면 주어진 요청에 50개의 행만 반환합니다. 다음 코드는 쿼리에 `take` 및 `skip` 절을 사용하여 반환된 데이터의 페이징을 구현하는 방법을 보여줍니다. 다음 쿼리를 실행하면 테이블에서 맨 위에 있는 세 개의 항목을 반환합니다.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

`take(3)` 메서드는 쿼리 URI에서 `$top=3` 쿼리 옵션으로 변환되었습니다.

수정된 다음 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

모바일 서비스에 전송된 요청의 URI를 볼 수 있습니다. `skip(3)` 메서드는 쿼리 URI에서 `$skip=3` 쿼리 옵션으로 변환되었습니다.

이것은 하드 코드된 페이징 값을 `take` 및 `skip` 함수에 전달하는 간소화된 시나리오입니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다.

### <a name="selecting"></a>방법: 특정 열 선택

쿼리에 `select` 절을 추가하면 결과에 포함할 속성 집합을 지정할 수 있습니다. 예를 들어 다음 코드는 `todoItemTable`의 각 행에서 `id`, `complete` 및 `text` 속성을 반환합니다.

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

여기서 select 함수의 매개 변수는 반환하려는 테이블 열의 이름입니다.


지금까지 설명한 모든 함수는 가산적이므로 계속해서 호출할 수 있으며, 호출할 때마다 쿼리에 더 많은 영향이 있습니다. 한 가지 예를 더 들면 다음과 같습니다.

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>방법: ID를 기준으로 데이터 조회

`lookup` 함수는 `id` 값만 사용하여 데이터베이스에서 해당 ID를 사용하는 개체를 반환합니다. 데이터베이스 테이블은 정수 또는 문자열 `id` 열을 사용하여 만들어집니다. 문자열 `id` 열이 기본값입니다.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>OData 쿼리 작업 실행

모바일 서비스에서는 REST 쿼리를 작성 및 실행하는 데 OData 쿼리 URI 규칙을 사용합니다. 속성의 하위 문자열 검색 등 복잡한 필터 작업 같은 경우에는 기본 제공 쿼리 함수로 일부 OData 쿼리를 작성할 수 없습니다. 이처럼 복잡한 쿼리 유형의 경우 다음과 같이 `read` 함수로 유효한 OData 쿼리 옵션 문자열을 전달하면 됩니다.

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]원시 OData 쿼리 옵션 문자열을 `read` 함수에 제공하는 경우에는 쿼리 작성기 메서드를 같은 쿼리에서 사용할 수도 없습니다. 이 경우 전체 쿼리를 OData 쿼리 문자열로 작성해야 합니다. OData 시스템 쿼리 옵션에 대한 자세한 내용은 [OData 시스템 쿼리 옵션 참조]를 참조하세요.

##<a name="inserting"></a>방법: 모바일 서비스에 데이터 삽입

다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다. 클라이언트가 POST 요청을 모바일 서비스에 보내 데이터 행을 삽입하도록 요청합니다. 요청 본문에는 삽입할 데이터가 JSON 개체로 포함되어 있습니다.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

그러면 제공된 JSON 개체의 데이터가 테이블에 삽입됩니다. 삽입이 완료되면 호출할 콜백 함수를 지정할 수도 있습니다.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

###ID 값으로 작업

모바일 서비스는 테이블의 **id** 열에 대한 고유한 사용자 지정 문자열 값을 지원합니다. 이를 통해 응용 프로그램에서 메일 주소 또는 사용자 이름과 같은 사용자 지정 값을 ID에 사용할 수 있습니다. 예를들어 다음 코드는 고유한 ID가 이메일 주소인 경우 새 항목을 JSON 개체로 삽입합니다.

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

문자열 ID는 다음과 같은 이점을 제공합니다.

+ ID는 데이터베이스에 대한 왕복 없이도 생성됩니다.
+ 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
+ 응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

문자열 ID 값이 삽입된 레코드에 아직 설정되지 않은 경우 모바일 서비스는 해당 ID에 대한 고유한 값을 생성합니다. 클라이언트 또는 .NET 백 엔드에서 고유한 ID 값을 생성하는 방법에 대한 자세한 내용은 [방법: 고유한 ID 값 생성](mobile-services-how-to-use-server-scripts.md#generate-guids)을 참조하세요.

또한 테이블에 정수 ID를 사용할 수 있습니다. 정수 ID를 사용하려면 `mobile table create` 명령으로 `--integerId` 옵션을 사용하여 테이블을 만들어야 합니다. 이 명령은 Azure용 CLI(명령줄 인터페이스)와 함께 사용됩니다. CLI 사용에 대한 자세한 내용은 [모바일 서비스 테이블 관리 CLI](../virtual-machines-command-line-tools.md#Mobile_Tables)(영문)를 참조하세요.

##<a name="modifying"></a>방법: 모바일 서비스의 데이터 수정

다음 코드는 테이블의 데이터를 업데이트하는 방법을 보여 줍니다. 클라이언트가 PATCH 요청을 모바일 서비스에 보내 데이터 행을 업데이트하도록 요청합니다. 요청 본문에는 업데이트할 특정 필드가 JSON 개체로 포함되어 있습니다. `todoItemTable` 테이블의 기존 항목을 업데이트합니다.

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	})

첫 번째 매개 변수는 ID에 지정된 대로 테이블에서 업데이트할 인스턴스를 지정합니다.

업데이트가 완료되면 호출할 콜백 함수를 지정할 수도 있습니다.

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="deleting"></a>방법: 모바일 서비스의 데이터 삭제

다음 코드는 테이블에서 데이터를 삭제하는 방법을 보여 줍니다. 클라이언트가 DELETE 요청을 모바일 서비스에 보내 데이터 행을 삭제하도록 요청합니다. todoItemTable 테이블의 기존 항목을 삭제합니다.

	todoItemTable.del({
	   id: idToDelete
	})

첫 번째 매개 변수는 ID에 지정된 대로 테이블에서 삭제할 인스턴스를 지정합니다.

삭제가 완료되면 호출할 콜백 함수를 지정할 수도 있습니다.

	todoItemTable.del({
	   id: idToDelete
	}).done(function () {
	   /* Do something */
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="binding"></a>방법: 사용자 인터페이스에 데이터 표시

이 섹션에서는 반환된 데이터 개체를 UI 요소를 사용해서 표시하는 방법을 보여 줍니다. `todoItemTable`의 항목을 쿼리하여 매우 간단한 목록에 표시하려면 다음 예제 코드를 실행하면 됩니다. 어떤 종류의 선택이나 필터링 또는 정렬도 하지 않습니다.

	var query = todoItemTable;

	query.read().then(function (todoItems) {
	   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
	   var listOfItems = document.getElementById('placeToInsert');
	   for (var i = 0; i < todoItems.length; i++) {
	      var li = document.createElement('li');
	      var div = document.createElement('div');
	      div.innerText = todoItems[i].text;
	      li.appendChild(div);
	      listOfItems.appendChild(li);
	   }
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Windows 스토어 앱에서 쿼리 결과를 사용하여 [WinJS.Binding.List] 개체를 만들 수 있으며, 이 개체를 [ListView] 개체의 데이터 원본으로 바인딩할 수 있습니다. 자세한 내용은 [데이터 바인딩(JavaScript 및 HTML을 사용한 Windows 스토어 앱)](영문)을 참조하세요.

##<a name="#custom-api"></a>방법: 사용자 지정 API 호출

사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다. 모바일 서비스에서 사용자 지정 API를 만드는 방법의 예는 [방법: 사용자 지정 API 끝점 정의](mobile-services-dotnet-backend-define-custom-api.md)를 참조하세요.

**MobileServiceClient**에서 [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) 메서드를 호출하여 클라이언트에서 사용자 지정 API를 호출합니다. 예를 들어 다음 코드 줄은 모바일 서비스의 **completeAll** API로 POST 요청을 보냅니다.

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });

 
**invokeApi**의 보다 실질적인 예와 자세한 설명을 확인하려면 [Azure 모바일 서비스 클라이언트 SDK의 사용자 지정 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)를 참조하세요.

##<a name="authentication"></a>방법: 사용자 인증

모바일 서비스는 Facebook, Google, Microsoft 계정 및 Twitter와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작] 자습서를 참조하세요.

>[AZURE.NOTE]PhoneGap 또는 Cordova 앱에서 인증을 사용하는 경우 프로젝트에 다음 플러그 인도 추가해야 합니다.
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


두 가지의 인증 흐름, 즉 _서버 흐름_과 _클라이언트 흐름_이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하므로 Single Sign-On과 같은 장치 특정 기능을 통해 심도 깊은 통합이 가능합니다.

###서버 흐름
모바일 서비스가 Windows 스토어 또는 HTML5 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, 모바일 서비스에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다. 자세한 내용은 [앱에 인증 추가](mobile-services-html-get-started-users.md) 자습서를 참조하세요.

ID 공급자를 등록하고 나면 공급자의 [MobileServiceAuthenticationProvider] 값을 사용하여 [LoginAsync 메서드]를 호출합니다. 예를 들어 Facebook으로 로그인하려면 다음 코드를 사용합니다.

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Facebook 이외의 ID 공급자를 사용하는 경우 위의 `login` 메서드에 전달된 값을 `microsoftaccount`, `facebook`, `twitter`, `google` 또는 `windowsazureactivedirectory` 중 하나로 변경합니다.

이 경우 모바일 서비스는 선택한 공급자의 로그인 페이지를 표시하고 ID 공급자 로그인 후 모바일 서비스 인증 토큰을 생성하여 OAuth 2.0 인증 흐름을 관리합니다. [login] 함수를 완료하면 사용자 ID와 모바일 서비스 인증 토큰을 각각 **userId** 및 **authenticationToken** 필드에 표시하는 JSON 개체(**user**)가 반환됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다. 자세한 내용은 [인증 토큰 캐시]를 참조하세요.

> [AZURE.NOTE]**Windows 스토어 앱** Windows 스토어 앱 사용자를 인증하는 데 Microsoft 계정 로그인 공급자를 사용하는 경우 앱 패키지를 모바일 서비스에도 등록해야 합니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Windows 스토어 앱 패키지를 등록하는 방법을 자세히 알아보려면 [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank")을 참조하세요. 패키지 정보가 모바일 서비스에 등록된 후에는 [useSingleSignOn](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank") 매개 변수에서 자격 증명을 다시 사용하도록 **true** 값을 제공하여 *login* 메서드를 호출합니다.

###클라이언트 흐름
앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 인증을 위해 모바일 서비스에 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그인 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다.

다음 예제는 Microsoft 계정을 사용하여 Windows 스토어 앱용 Single Sign-On을 지원하는 Live SDK를 사용합니다.

	WL.login({ scope: "wl.basic"}).then(function (result) {
	      client.login(
	            "microsoftaccount",
	            {"authenticationToken": result.session.authentication_token})
	      .done(function(results){
	            alert("You are now logged in as: " + results.userId);
	      },
	      function(error){
	            alert("Error: " + err);
	      });
	});

단순화된 이 예제는 [login] 함수를 호출하여 모바일 서비스에 제공된 토큰을 Live Connect에서 가져옵니다. Microsoft 계정을 사용하여 단일 로그인 환경을 제공하는 방법을 보여 주는 전체 예제는 [단일 로그인으로 앱 인증]을 참조하세요.

클라이언트 인증을 위해 Facebook 또는 Google API를 사용할 때는 예제가 약간 달라집니다.

	client.login(
	     "facebook",
	     {"access_token": token})
	.done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

이 예제에서는 각 공급자 SDK에서 제공된 토큰이 `token` 변수에 저장되어 있다고 가정합니다. 지금은 클라이언트 인증에 Twitter를 사용할 수 없습니다. 지금은 Microsoft Azure Active Directory를 JavaScript 백엔드와 함께 클라이언트 인증에 사용할 수 없습니다.

###인증 토큰 캐시
일부 경우, 사용자가 처음으로 인증된 후에 login 메서드 호출을 방지할 수 있습니다. [sessionStorage] 또는 [localStorage]를 사용하여 사용자가 처음 로그인할 때 현재 사용자 ID를 캐시하고 이후에 매번 캐시에서 해당 사용자 ID가 이미 있는지 여부를 확인할 수 있습니다. 캐시가 비어 있거나 호출이 실패하는 경우(현재 로그인 세션이 만료되었음을 의미함)에도 로그인 프로세스를 거쳐야 합니다.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;

##<a name="push-notifications"></a>방법: 푸시 알림 등록

앱이 PhoneGap 또는 Apache Cordova HTML/JavaScript 앱인 경우, 네이티브 모바일 플랫폼을 사용하면 장치에 푸시 알림을 받을 수 있습니다. [Azure 모바일 서비스용 Apache Cordova 플러그인](https://github.com/Azure/azure-mobile-services-cordova)을 사용하면 Azure 알림 허브로 푸시 알림을 등록할 수 있습니다. 사용되는 특정 알림 서비스는 코드가 실행되는 네이티브 장치 플랫폼에 따라 다릅니다. 이 작업을 수행하는 방법의 예는 [Microsoft Azure를 사용하여 Cordova 앱에 푸시 알림](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle) 샘플을 참조하세요.

>[AZURE.NOTE]이 플러그인은 현재 iOS 및 Android 장치만 지원합니다. Windows 장치를 포함하는 솔루션의 경우, [알림 허브 통합을 사용하여 PhoneGap 앱에 푸시 알림](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx)을 참조하세요.

##<a name="errors"></a>방법: 오류 처리

모바일 서비스에서 오류가 발생하고, 오류의 유효성을 검사하고, 오류를 해결하는 방법이 몇 가지 있습니다.

예를 들어 서버 스크립트를 모바일 서비스에 등록하여 삽입 및 업데이트할 데이터에 대해 유효성 확인 및 데이터 수정 등 다양한 작업을 수행하는 데 사용할 수 있습니다. 데이터 유효성 검사 및 수정을 수행하는 서버 스크립트를 다음과 같이 정의하고 등록합니다.

	function insert(item, user, request) {
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
	      request.execute();
	   }
	}

이 서버 쪽 스크립트는 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부합니다.

모바일 서비스가 서버 쪽에서 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 HTML 앱을 업데이트할 수 있습니다.

	todoItemTable.insert({
	   text: itemText,
	   complete: false
	})
	   .then(function (results) {
	   alert(JSON.stringify(results));
	}, function (error) {
	   alert(JSON.parse(error.request.responseText).error);
	});


추가로 조작해 보기 위해, 데이터에 액세스할 때마다 두 번째 인수로서 오류 처리기를 전달합니다.

	function handleError(message) {
	   if (window.console && window.console.error) {
	      window.console.error(message);
	   }
	}

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>방법: promises 사용

Promise는 완료할 작업을 아직 계산되지 않은 값으로 예약하는 메커니즘을 제공합니다. 비동기 API 조작을 관리하는 추상적 개념입니다.

`done` promise는 제공된 함수가 완료되거나 오류가 발생하면 곧바로 실행됩니다. `then` promise와 달리, 함수 내부에서 처리되지 않은 오류를 throw하며 처리기가 실행을 완료하고 나면 이 함수는 오류 상태의 promise로서 then으로부터 반환된 오류를 throw합니다. 자세한 내용은 [done](영문)을 참조하세요.

	promise.done(onComplete, onError);

다음과 같습니다.

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

`then` promise와 `done` promise는 동일합니다. 하지만 `then` promise와 달리, `done`은(는) 함수 내부에서 처리되지 않은 오류를 반드시 throw합니다. `then`에 오류 처리기를 제공하지 않은 경우 연산에서 오류가 발생하면 오류가 throw되지 않고 대신 오류 상태의 promise가 반환됩니다. 자세한 내용은 [then](영문)을 참조하세요.

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

다음과 같습니다.

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

다양한 방법으로 promise를 사용할 수 있습니다. 이전 `then` 함수에서 반환되는 promise에 대해 `then` 또는 `done`을(를) 호출하는 방법으로 promise 연산을 연결할 수 있습니다. 연산의 중간 단계에는 `.then().then()`을 사용하고(예: `then`) 연산의 마지막 단계에는 `done`을(를) 사용합니다(예: `.then().then().done()`). `then`은(는) promise를 반환하기 때문에 여러 `then` 함수를 연결할 수 있습니다. undefined를 반환하기 때문에 두 개 이상의 `done` 메서드를 연결할 수 없습니다. [then과 done의 차이에 대해 자세히 알아보세요](영문).

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>방법: 클라이언트 요청 헤더 사용자 지정

`withFilter` 함수를 사용하면 보낼 요청의 임의의 속성을 필터 내부에서 읽고 써서 사용자 지정 요청 헤더를 보낼 수 있습니다. 서버 쪽 스크립트에 필요하거나 이 스크립트를 강화할 수 있는 경우 이런 사용자 지정 HTTP 헤더를 추가할 수 있습니다.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

필터는 요청 헤더를 사용자 지정하는 것 외에도 다양하게 사용됩니다. 요청 검사나 변경, 응답 검사나 변경, 네트워킹 호출 무시, 여러 호출 보내기 등에 사용할 수 있습니다.

##<a name="hostnames"></a>방법: 크로스-원본 자원 공유 사용

요청을 조작하여 모바일 서비스에 보낼 수 있는 웹 사이트를 제어하려면 호스트하는 데 사용하는 웹 사이트의 호스트 이름을 CORS(Cross Origin Resource Sharing) 허용 목록에 추가해야 합니다. JavaScript 백 엔드 모바일 서비스의 경우, [Azure 관리 포털](https://manage.windowsazure.com)의 구성 탭에서 허용 목록을 구성할 수 있습니다. 필요한 경우 와일드카드를 사용할 수 있습니다. 기본적으로 새 모바일 서비스는 `localhost`에서만 액세스를 허용하도록 브라우저에 지시하며, CORS(크로스-원본 자원 공유)는 브라우저에서 외부 호스트 이름으로 실행되는 JavaScript 코드가 모바일 서비스와 조작할 수 있게 합니다. 이 구성은 WinJS 응용 프로그램의 경우에는 필요 없습니다.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[then과 done의 차이에 대해 자세히 알아보세요]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[데이터 바인딩(JavaScript 및 HTML을 사용한 Windows 스토어 앱)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[단일 로그인으로 앱 인증]: mobile-services-windows-store-javascript-single-sign-on.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[OData 시스템 쿼리 옵션 참조]: http://go.microsoft.com/fwlink/p/?LinkId=444502
[Call a custom API from the client]: mobile-services-html-call-custom-api.md
 

<!---HONumber=Sept15_HO3-->