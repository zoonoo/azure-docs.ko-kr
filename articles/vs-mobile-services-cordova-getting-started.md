<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 변경된 내용

### 참조 추가됨

모든 다중 장치 하이브리드 앱에 포함된 Azure 모바일 서비스 클라이언트 플러그 인이 사용하도록 설정되었습니다.

### 모바일 서비스에 대한 연결 문자열 값

services\\mobileServices\\settings 아래에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 새 JavaScript(.js) 파일이 MobileServiceClient를 사용하여 생성되었습니다. 이 파일에는 모바일 서비스 클라이언트 개체의 초기화가 포함되어 있는데, 이 코드는 다음과 유사합니다.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### 모바일 서비스 시작

###### 테이블에 대한 참조 가져오기

다음 코드는 TodoItem에 대한 데이터를 포함하는 테이블에 대한 참조를 가져옵니다. 이 참조는 데이터 테이블을 읽고 업데이트하는 후속 작업에서 사용할 수 있습니다. TodoItem 테이블은 모바일 서비스를 만들 때 자동으로 만들어집니다.

    var todoTable = mobileServiceClient.getTable('TodoItem');

이 예제가 작동하려면 테이블에 대한 사용 권한을 **응용 프로그램 키가 있는 모든 사용자**로 설정해야 합니다. 나중에 인증을 설정할 수 있습니다. [인증 시작][인증 시작]을 참조하세요.

###### 항목 추가

데이터 테이블에 새 항목을 삽입합니다. 자동으로 ID(문자열 유형의 GUID)가 새 행의 기본 키로 만들어집니다. 반환된 [Promise][Promise] 개체에 대한 [done][Promise] 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### 테이블 읽기/쿼리

다음 코드는 텍스트 필드를 기준으로 정렬된 모든 항목의 테이블을 쿼리합니다. success 처리기에 쿼리 결과를 처리하는 코드를 추가할 수 있습니다. 이 경우 항목의 로컬 배열이 업데이트됩니다.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

where 메서드를 사용하여 쿼리를 수정할 수 있습니다. 다음은 완료된 항목을 필터링하는 예제입니다.

    todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

사용할 수 있는 쿼리의 추가 예제는 [query][query] 개체를 참조하세요.

###### 항목 업데이트

데이터 테이블의 행을 업데이트합니다. 이 코드에서는 모바일 서비스가 응답하면 항목이 목록에서 제거됩니다. 반환된 [Promise][Promise] 개체에 대한 [done][Promise] 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### 항목 삭제

del 메서드를 사용하여 데이터 테이블의 행을 삭제합니다. 반환된 [Promise][Promise] 개체에 대한 [done][Promise] 메서드를 호출하여 삽입된 개체의 복사본을 가져오고 오류를 처리합니다.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [인증 시작]: http://azure.microsoft.com/ko-kr/documentation/articles/mobile-services-html-get-started-users/
  [Promise]: 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
