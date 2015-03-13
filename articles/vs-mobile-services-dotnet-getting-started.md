<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [변경된 내용](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

## 모바일 서비스 시작(.NET 프로젝트)

이 예에서 코드를 따르기 위해 해야 할 첫 번째 단계는 연결할 모바일 서비스 유형에 따라 다릅니다.

JavaScript 백엔드 모바일 서비스의 경우 TodoItem이라는 이름의 테이블을 만듭니다.  테이블을 만들려면 서버 탐색기의 Azure 노드에서 모바일 서비스를 찾고, 모바일 서비스 노드를 마우스 오른쪽으로 클릭하여 상황에 맞는 메뉴를 연 다음 **테이블 만들기**를 선택합니다. "TodoItem"을 테이블 이름으로 입력합니다.

대신 .NET 백엔드 모바일 서비스가 있는 경우 Visual Studio에서 사용자를 위해 만든 기본 프로젝트 템플릿에 TodoItem 테이블이 이미 있습니다. 하지만 이 테이블을 Azure에 게시해야 합니다. 테이블을 게시하려면 솔루션 탐색기에서 모바일 서비스 프로젝트의 상황에 맞는 메뉴를 연 다음 **웹 게시**를 선택합니다. 기본값을 사용하려면 **게시** 단추를 선택합니다.

#####테이블에 대한 참조 가져오기

다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다. 모바일 서비스가 쿼리에 응답하여 보내는 JSON을 해석하려면 특성이 설정된 TodoItem 클래스가 필요합니다.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

이 코드는 테이블의 사용 권한이 **응용 프로그램 키가 있는 모든 사용자**로 설정된 경우 작동합니다. 모바일 서비스를 보호하기 위해 사용 권한을 변경하는 경우 사용자 인증 지원을 추가해야 합니다. [인증 시작](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)을 참조하세요.

#####항목 추가 

데이터 테이블에 새 항목을 삽입합니다.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####테이블 읽기/쿼리 

다음 코드에서는 테이블의 모든 항목을 쿼리합니다. 기본적으로 50개 항목인 데이터의 첫 번째 페이지만 반환합니다. 선택적 매개 변수이므로 원하는 페이지 크기를 전달할 수 있습니다.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####항목 업데이트

데이터 테이블의 행을 업데이트합니다. 매개 변수 항목은 업데이트할 TodoItem 개체입니다.

	await todoTable.UpdateAsync(item);

#####항목 삭제

데이터베이스의 행을 삭제합니다. 매개 변수 항목은 삭제할 TodoItem 개체입니다.

	await todoTable.DeleteAsync(item);


[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
