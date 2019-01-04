---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 32d09722e8c396a64451018ac92fbc7bc072f461
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132941"
---
## <a name="create-client"></a>클라이언트 연결 만들기
`WindowsAzure.MobileServiceClient` 개체를 만들어 클라이언트 연결을 만듭니다.  `appUrl` 을 모바일 앱에 대한 URL로 바꿉니다.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>테이블 작업
데이터에 액세스하거나 데이터를 업데이트하려면 백 엔드 테이블에 대한 참조를 만듭니다. `tableName` 을 테이블의 이름으로 바꿉니다.

```
var table = client.getTable(tableName);
```

테이블 참조가 있는 경우 테이블을 사용하여 더 많은 일을 할 수 있습니다.

* [테이블 쿼리](#querying)
  * [데이터 필터링](#table-filter)
  * [데이터를 통한 페이징](#table-paging)
  * [데이터 정렬](#sorting-data)
* [데이터 삽입](#inserting)
* [데이터 수정](#modifying)
* [데이터 삭제](#deleting)

### <a name="querying"></a>방법: 테이블 참조 쿼리
테이블 참조가 있는 경우 이를 사용하여 서버에서 데이터를 쿼리할 수 있습니다.  쿼리는 "LINQ와 유사한" 언어로 만들어집니다.
테이블에서 모든 데이터를 반환하려면 다음 코드를 사용합니다.

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

결과에 대해 success 함수가 호출됩니다.  success 함수에 `for (var i in results)`를 사용하지 마세요. 다른 쿼리 함수(예: `.includeTotalCount()`)가 사용될 경우 결과에 포함된 정보를 반복합니다.

쿼리 구문에 대한 자세한 내용은 [쿼리 개체 설명서]를 참조하세요.

#### <a name="table-filter"></a>서버에서 데이터 필터링
테이블 참조에서 `where` 절을 사용할 수 있습니다.

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

개체를 필터링하는 함수도 사용할 수 있습니다.  이 경우 `this` 변수가 필터링되는 현재 개체에 할당됩니다.  다음 코드는 이전 예와 기능적으로 동일합니다.

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>데이터를 통한 페이징
`take()` 및 `skip()` 메서드를 활용합니다.  예를 들어 테이블을 100개 행 레코드로 분할하려는 경우 다음과 같습니다.

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` 메서드는 결과 개체에 totalCount 필드를 추가하는 데 사용됩니다.  totalCount 필드는 페이징이 사용되지 않는 경우 반환되는 총 레코드 수로 채워집니다.

다음으로 페이지 변수 및 몇 가지 UI 단추를 사용하여 페이지 목록을 제공할 수 있습니다. 각 페이지에 대해 새 레코드를 로드하는 데 `loadPage()`를 사용합니다.  이미 로드된 레코드에 대한 액세스 속도를 높이려면 캐싱을 구현합니다.

#### <a name="sorting-data"></a>방법: 정렬된 데이터 반환
`.orderBy()` 또는 `.orderByDescending()` 쿼리 메서드 사용:

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

쿼리 개체에 대한 자세한 내용은 [쿼리 개체 설명서]를 참조하세요.

### <a name="inserting"></a>방법: 데이터 삽입
적절한 날짜로 JavaScript 개체를 만들고 `table.insert()`를 비동기적으로 호출합니다.

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

성공적으로 삽입되면 동기화 작업에 필요한 추가 필드와 함께 삽입된 항목이 반환됩니다.  이후 업데이트를 위해 사용자 고유의 캐시를 이 정보로 업데이트합니다.

Azure Mobile Apps Node.js 서버 SDK는 개발 목적으로 동적 스키마를 지원합니다.  동적 스키마를 통해 삽입 또는 업데이트 작업에 열을 지정하여 테이블에 열을 추가할 수 있습니다.  애플리케이션을 프로덕션으로 전환하기 전에 동적 스키마를 해제하는 것이 좋습니다.

### <a name="modifying"></a>방법: 데이터 수정
`.insert()` 메서드와 마찬가지로 업데이트 개체를 만든 후 `.update()`를 호출해야 합니다.  업데이트 개체는 업데이트할 레코드의 ID를 포함해야 하며 레코드를 읽거나 `.insert()`를 호출할 때 ID를 얻을 수 있습니다.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>방법: 데이터 삭제
레코드를 삭제하려면 `.del()` 메서드를 호출합니다.  개체 참조에 ID를 전달합니다.

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
