<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 변경된 내용

###### 참조 추가됨

Microsoft Azure 모바일 서비스 라이브러리가 MobileServices.js 파일 형식으로 프로젝트에 추가되었습니다.

###### 모바일 서비스에 대한 연결 문자열 값

services\\mobileServices\\settings 폴더에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 MobileServiceClient를 사용하여 생성되었습니다.

### 모바일 서비스 시작

###### 테이블에 대한 참조 가져오기

클라이언트 개체가 프로젝트에 이미 추가되었습니다. 이 개체는 모바일 서비스 이름에 "Client"가 추가된 이름을 사용합니다. 다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

###### 항목 추가

데이터 테이블에 새 항목을 삽입합니다. 자동으로 ID(문자열 유형의 GUID)가 새 행의 기본 키로 만들어집니다. 모바일 서비스 인프라에서 ID 열을 사용하므로 이 열의 유형을 변경하지 마세요.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

###### 테이블 읽기/쿼리

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

사용할 수 있는 쿼리의 추가 예제는 [query 개체][query 개체]를 참조하세요.

###### 항목 업데이트

데이터 테이블의 행을 업데이트합니다. 이 예제에서 todoItem은 업데이트된 항목이고 항목은 모바일 서비스에서 반환된 항목과 동일합니다. 모바일 서비스가 응답할 때 로컬 todoItems 목록의 항목이 [splice][splice](영문) 메서드를 사용하여 업데이트됩니다. 반환된 [Promise]() 개체에 대한 [done]() 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

###### 항목 삭제

데이터 테이블의 행을 삭제합니다. 반환된 [Promise]() 개체에 대한 [done]() 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

  [query 개체]: http://msdn.microsoft.com/library/azure/jj613353.aspx
  [splice]: http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx

