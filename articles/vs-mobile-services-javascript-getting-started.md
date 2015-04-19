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
> - [시작](vs-mobile-services-javascript-getting-started.md)
> - [변경된 내용](vs-mobile-services-javascript-what-happened.md)

## 모바일 서비스 시작

이 예에서 코드를 따르기 위해 해야 할 첫 번째 단계는 연결할 모바일 서비스 유형에 따라 다릅니다.

JavaScript 백엔드 모바일 서비스의 경우 TodoItem이라는 이름의 테이블을 만듭니다.  테이블을 만들려면 서버 탐색기의 Azure 노드에서 모바일 서비스를 찾고, 모바일 서비스 노드를 마우스 오른쪽으로 클릭하여 상황에 맞는 메뉴를 연 다음 **테이블 만들기**를 선택합니다. "TodoItem"을 테이블 이름으로 입력합니다.

대신 .NET 백엔드 모바일 서비스가 있는 경우 Visual Studio에서 사용자를 위해 만든 기본 프로젝트 템플릿에 TodoItem 테이블이 이미 있습니다. 하지만 이 테이블을 Azure에 게시해야 합니다. 테이블을 게시하려면 솔루션 탐색기에서 모바일 서비스 프로젝트의 상황에 맞는 메뉴를 연 다음 **웹 게시**를 선택합니다. 기본값을 사용하려면 **게시** 단추를 선택합니다.

#####테이블에 대한 참조 가져오기

클라이언트 개체가 프로젝트에 이미 추가되었습니다.  이 개체는 모바일 서비스 이름에 "Client"가 추가된 이름을 사용합니다. 다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

#####항목 추가 

데이터 테이블에 새 항목을 삽입합니다. 자동으로 ID(문자열 유형의 GUID)가 새 행의 기본 키로 만들어집니다. 모바일 서비스 인프라에서 ID 열을 사용하므로 이 열의 유형을 변경하지 마세요.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

#####테이블 읽기/쿼리

다음 코드는 테이블에서 모든 항목을 쿼리하고, 로컬 컬렉션을 업데이트하고, 결과를 UI 요소 listItems에 바인딩합니다.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

where 메서드를 사용하여 쿼리를 수정할 수 있습니다. 다음은 완료된 항목을 필터링하는 예제입니다.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

사용할 수 있는 쿼리의 추가 예제는 [query 개체](http://msdn.microsoft.com/library/azure/jj613353.aspx)를 참조하세요.

#####항목 업데이트

데이터 테이블의 행을 업데이트합니다. 이 예제에서 todoItem은 업데이트된 항목이고 항목은 모바일 서비스에서 반환된 항목과 동일합니다. 모바일 서비스가 응답할 때 로컬 todoItems 목록의 항목이 [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx) 메서드를 사용하여 업데이트됩니다. 반환된 [Promise]() 개체에 대한 [done]() 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####항목 삭제

데이터 테이블의 행을 삭제합니다. 반환된 [Promise]() 개체에 대한 [done]() 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

	todoTable.delete(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
