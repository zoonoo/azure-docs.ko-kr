<properties 
	pageTitle="" 
	description="Cordova 프로젝트에서 Azure 모바일 서비스를 시작하기 위해 수행할 수 있는 첫 단계에 대해 설명합니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="patshea123"/>

# 모바일 서비스 시작(Cordova 프로젝트)

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

이 예에서 코드를 따르기 위해 해야 할 첫 번째 단계는 연결할 모바일 서비스 유형에 따라 다릅니다.

- JavaScript 백엔드 모바일 서비스의 경우 TodoItem이라는 이름의 테이블을 만듭니다. 테이블을 만들려면 서버 탐색기의 Azure 노드에서 모바일 서비스를 찾고, 모바일 서비스 노드를 마우스 오른쪽으로 클릭하여 상황에 맞는 메뉴를 연 다음 **테이블 만들기**를 선택합니다. "TodoItem"을 테이블 이름으로 입력합니다.

- .NET 백엔드 모바일 서비스가 있는 경우 Visual Studio에서 사용자를 위해 만든 기본 프로젝트 템플릿에 TodoItem 테이블이 이미 있습니다. 하지만 이 테이블을 Azure에 게시해야 합니다. 테이블을 게시하려면 솔루션 탐색기에서 모바일 서비스 프로젝트의 상황에 맞는 메뉴를 연 다음 **웹 게시**를 선택합니다. 기본값을 사용하려면 **게시** 단추를 선택합니다.



#####테이블에 대한 참조 만들기

다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다. TodoItem 테이블은 모바일 서비스를 만들 때 자동으로 만들어집니다.

    var todoTable = mobileServiceClient.getTable('TodoItem');

이 예제가 작동하려면 테이블에 대한 사용 권한을 **응용 프로그램 키가 있는 모든 사용자**로 설정해야 합니다. 나중에 인증을 설정할 수 있습니다. [인증 시작](mobile-services-html-get-started-users.md)을 참조하세요.

#####테이블에 항목 추가

데이터 테이블에 새 항목을 삽입합니다. 자동으로 ID(문자열 유형의 GUID)가 새 행의 기본 키로 만들어집니다. 반환된 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 개체에 대한 `done()` 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }
    
    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

#####테이블 읽기 또는 쿼리

다음 코드는 텍스트 필드를 기준으로 정렬된 모든 항목의 테이블을 쿼리합니다. success 처리기에 쿼리 결과를 처리하는 코드를 추가할 수 있습니다. 이 경우 항목의 로컬 배열이 업데이트됩니다.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

where 메서드를 사용하여 쿼리를 수정할 수 있습니다. 다음은 완료된 항목을 필터링하는 예제입니다.

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

사용할 수 있는 쿼리의 추가 예제는 [query](http://msdn.microsoft.com/library/azure/jj613353.aspx) 개체를 참조하세요.

#####테이블 항목 업데이트

데이터 테이블의 행을 업데이트합니다. 이 코드에서는 모바일 서비스가 응답하면 항목이 목록에서 제거됩니다. 반환된 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 개체에 대한 `done()` 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####테이블 항목 삭제

**del** 메서드를 사용하여 데이터 테이블의 행을 삭제합니다. 반환된 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 개체에 대한 `done()` 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[모바일 서비스에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO4-->