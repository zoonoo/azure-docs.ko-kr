<properties urlDisplayName="Table Service" pageTitle="테이블 저장소 사용 방법(Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />

# Node.js에서 테이블 서비스를 사용하는 방법

이 가이드에서는 Azure 테이블 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리**가 포함됩니다. 테이블에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

## 목차

* [테이블 서비스 정의][]   
* [개념][]   
* [Azure 저장소 계정 만들기](#create-account)
* [Node.js 응용 프로그램 만들기](#create-app)
* [저장소에 액세스하도록 응용 프로그램 구성](#configure-access)
* [Azure 저장소 연결 설정](#setup-connection-string)  
* [방법: 테이블 만들기](#create-table)
* [방법: 테이블에 엔터티 추가](#add-entity)
* [방법: 엔터티 업데이트](#update-entity)
* [방법: 엔터티 그룹 작업](#change-entities)
* [방법: 엔터티 검색](#query-for-entity)
* [방법: 엔터티 집합 쿼리](#query-set-entities)
* [방법: 엔터티 삭제](#delete-entity)
* [방법: 테이블 삭제](#delete-table)   
* [방법: 공유 액세스 서명 작업](#sas)
* [다음 단계][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>Azure 저장소 계정 만들기</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Node.js 응용 프로그램 만들기

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기], [Node.js 클라우드 서비스][Node.js Cloud Service](Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트]를 참조하세요.

## <a name="configure-access"> </a>저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함되어 있는
Node.js용 Azure Storage SDK가 필요합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure-storage**를 입력합니다. 그러면 다음과 같이 출력됩니다.

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 저장소에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure-storage** 패키지를 찾습니다.

### 패키지 가져오기

메모장이나 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의 **server.js** 파일의 맨 위에 다음을 추가합니다.

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Azure 저장소 연결 설정

Azure 모듈은 AZURE\_STORAGE\_ACCOUNT 및 AZURE\_STORAGE\_ACCESS\_KEY 또는 AZURE\_STORAGE\_CONNECTION\_STRING 환경 변수에서 Azure 저장소 계정에 연결하는 데 필요한 정보를 읽습니다. 이러한 환경 변수가 설정되어 있지 않은 경우 **TableService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는[Node.js 웹 응용 프로그램 및 저장소]를 참조하세요.

## <a name="create-table"> </a>테이블을 만드는 방법

다음 코드에서는 **TableService** 개체를 만들고 새 테이블을 만드는 데 사용합니다. **server.js** 위쪽에 다음을 추가합니다.

    var tableSvc = azure.createTableService();

**createTableIfNotExists**를 호출하면 지정된 이름을 사용하는 테이블이 없는 경우 새 테이블을 만듭니다. 다음 예제에서는 'mytable'이라는 새 테이블(없는 경우)을 만듭니다.

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

새 테이블이 만들어지면 `result`는 `true`가 되고, 테이블이 이미 있으면 `false`가 됩니다. `response`에는 요청에 대한 정보가 포함됩니다.

###필터

**TableService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

		function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

		function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter** 필터 두 개가 포함되어 있습니다. 다음에서는 **ExponentialRetryPolicyFilter**를 사용하는 **TableService** 개체를 만듭니다.

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>테이블에 엔터티를 추가하는 방법

엔터티를 추가하려면 먼저 엔터티 속성을 정의하는 개체를 만듭니다. 모든 엔터티에는 엔터티의 고유 식별자인 **PartitionKey**와 **RowKey**가 있어야 합니다.

* **PartitionKey** - 엔터티가 저장되는 파티션을 결정합니다.

* **RowKey** - 파티션 내에서 엔터티를 고유하게 식별합니다.

**PartitionKey**와 **RowKey**는 모두 문자열 값이어야 합니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](http://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.

다음은 엔터티를 정의하는 경우의 예입니다. **dueDate**는 **Edm.DateTime** 형식으로 정의됩니다. 유형 지정은 선택적이며 지정하지 않을 경우 유형이 유추됩니다.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [WACOM.NOTE] 각 레코드에는 엔터티가 삽입되거나 업데이트될 경우 Azure에서 설정되는 **Timestamp** 필드도 있습니다.

**entityGenerator**를 사용하여 엔터티를 만들 수도 있습니다. 다음 예제에서는 **entityGenerator**를 사용하여 동일한 작업 엔터티를 만듭니다.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

테이블에 엔터티를 추가하려면 엔터티 개체를
**insertEntity** 메서드에 전달합니다.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

작업에 성공하면 `result`에는 삽입된 레코드의 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 가 포함되고, `response`에는 작업에 대한 정보가 포함됩니다.

> [WACOM.NOTE] 기본적으로 **insertEntity**는 삽입된 엔터티를 `response` 정보의 일부로 반환하지 않습니다. 이 엔터티에서 다른 작업을 수행할 계획이거나 정보를 캐시하고 싶은 경우에는 `result`의 일부로 반환하면 유용합니다. 다음과 같이 **echoContent**를 사용하도록 설정하면 됩니다.
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>엔터티를 업데이트하는 방법

다음과 같은 여러 메서드를 사용하여 기존 엔터티를 업데이트할 수 있습니다.

* **updateEntity** - 기존 엔터티를 바꾸어서 업데이트합니다.

* **mergeEntity** - 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다.

* **insertOrReplaceEntity** - 기존 엔터티를 바꾸어서 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

* **insertOrMergeEntity** - 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

다음 예제에서는 **updateEntity**를 사용하여 엔터티를 업데이트하는 방법을 보여 줍니다.

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] 기본적으로는 엔터티를 업데이트할 때 업데이트할 데이터를 다른 프로세서에서 이전에 수정했는지 확인하지 않습니다. 동시 업데이트를 지원하려면:
> 
> 1. 업데이트할 개체의 ETag를 가져옵니다. 모든 엔터티 관련 작업에 대해 `response`의 일부로 반환되며 `response['.metadata'].etag`를 통해 검색할 수 있습니다.
> 
> 2. 엔터티에서 업데이트 작업을 수행할 때 이전에 검색한 ETag 정보를 새 엔터티에 추가합니다. 예를 들면 다음과 같습니다.
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. 업데이트 작업을 수행합니다. ETag 값을 검색한 후에 응용 프로그램의 다른 인스턴스 등에서 엔터티가 수정된 경우에는 요청에 지정된 업데이트 조건이 충족되지 않았다는 내용의 `error`가 반환됩니다.
    
**updateEntity** 및 **mergeEntity**를 사용할 때 업데이트할 엔터티가 없으면 업데이트 작업이 실패합니다. 따라서 엔터티의 존재 여부에 관계없이 엔터티를 저장하려면 **insertOrReplaceEntity** 또는 **insertOrMergeEntity**를 대신 사용해야 합니다.

성공적인 업데이트 작업에 대한 `result`에는 업데이트된 엔터티의 **Etag**가 포함됩니다.

## <a name="change-entities"> </a>엔터티 그룹 작업 방법
서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 이렇게 하려면 **TableBatch** 클래스를 사용하여 배치를 만든 다음 **TableService**의 **executeBatch** 메서드를 사용하여 일괄 처리 작업을 수행합니다.

 The following example demonstrates submitting two entities in a batch:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

일괄 처리 작업에 성공하면 `result`에는 일괄 처리의 각 작업에 대한 정보가 포함됩니다.

###일괄 처리 작업

배치에 추가된 작업은 `operations` 속성을 확인하여 검색할 수 있습니다. 작업에 다음 메서드를 사용할 수도 있습니다.

* **clear** - 배치에서 모든 작업을 지웁니다.

* **getOperations** - 배치에서 작업을 가져옵니다.

* **hasOperations** - 배치에 작업이 포함되어 있으면 true를 반환합니다.

* **removeOperations** - 작업을 제거합니다.

* **size** - 배치에 있는 작업의 수를 반환합니다.

## <a name="query-for-entity"> </a>엔터티를 검색하는 방법

**PartitionKey**와 **RowKey**에 따라 특정 엔터티를 반환하려면 **retrieveEntity** 메서드를 사용합니다.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

이 작업이 완료되면 `result`에 엔터티가 포함됩니다.

## <a name="query-set-entities"> </a>엔터티 집합을 쿼리하는 방법

테이블을 쿼리하려면 **TableQuery** 개체를 통해
다음 절을 사용하는 쿼리 표현식을 작성합니다.

* **select** - 쿼리에서 반환할 필드입니다.

* **where** - where 절입니다.

	* **and** - `and` where 조건입니다.

	* **or** - `or` where 조건입니다.

* **top** - 가져올 항목의 수입니다.


다음 예에서는 PartitionKey가 'hometasks'인 상위 5개의 항목을 반환하는 쿼리를 구성합니다.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

**select**가 사용되지 않았기 때문에 모든 필드가 반환됩니다. 테이블에 대해 쿼리를 수행하려면 **queryEntities**를 사용합니다. 다음 예에서는 이 쿼리를 사용하여 'mytable'에서 엔터티를 반환합니다.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

성공할 경우 `result.entries`에는 쿼리와 일치하는 엔터티의 배열이 포함됩니다. 쿼리에서 엔터티를 모두 반환할 수 없는 경우 `result.continuationToken`이 *null*이 아닌 값이 되므로 **queryEntities**의 세 번째 매개 변수로 사용하여 더 많은 결과를 검색할 수 있습니다. 초기 쿼리의 경우 세 번째 매개 변수가 *null*이어야 합니다.

###엔터티 속성 하위 집합을 쿼리하는 방법

테이블 쿼리에서는 엔터티에서 일부 필드만 검색할 수 있습니다.
따라서 대역폭이 줄어들며 특히 큰 엔터티의 경우 쿼리 성능이 향상될 수 있습니다. **select** 절을 사용하고 반환할 필드의 이름을 전달합니다. 예를 들어 다음 쿼리에서는 **description** 및 **dueDate** 필드만 반환합니다.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>엔터티를 삭제하는 방법

파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다. 이
예제에서 **task1** 개체에는 삭제할 엔터티의 **RowKey**와
**PartitionKey** 값이 포함됩니다. 그런 다음 이 개체를 
**deleteEntity** 메서드에 전달합니다.

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [WACOM.NOTE] 항목을 삭제할 때 항목이 다른 프로세스에서 수정되지 않았는지 확인할 수 있도록 ETag를 사용하는 것이 좋습니다. ETag 사용에 대한 자세한 내용은 [방법: 엔터티 업데이트][]를 참조하세요.

## <a name="delete-table"> </a>테이블을 삭제하는 방법

다음 코드에서는 저장소 계정에서 테이블을 삭제합니다.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

테이블이 있는지 확실하지 않은 경우에는 **deleteTableIfExists**를 사용합니다.

## <a name="sas"></a>방법: 공유 액세스 서명 작업

SAS(공유 액세스 서명)는 저장소 계정 이름이나 키를 제공하지 않으면서 테이블에 세분화된 액세스 권한을 안전하게 제공하는 방법입니다. SAS는 모바일 앱에서 레코드를 쿼리하는 경우와 같이 데이터에 대해 제한된 액세스를 제공하는 경우에 자주 사용합니다.

클라우드 기반 서비스와 같이 신뢰할 수 있는 응용 프로그램에서는 **TableService**의 **generateSharedAccessSignature**를 사용하여 SAS를 생성하고 신뢰할 수 없거나 신뢰가 약한 응용 프로그램에 제공합니다. 예를 들면 모바일 앱이 여기에 해당됩니다. SAS는 SAS가 유효한 시작 및 종료 날짜와 SAS 소유자에게 부여되는 액세스 수준을 설명하는 정책을 사용하여 생성됩니다.

다음 예에서는 SAS 소유자가 테이블을 쿼리('r')할 수 있도록 허용하며 만든 후 100분이 지나면 만료되는 새 공유 액세스 정책을 생성합니다.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

SAS 소유자가 테이블에 액세스할 때 필요하므로 호스트 정보도 제공해야 합니다.

그러면 클라이언트 응용 프로그램이 **TableServiceWithSAS**에서 SAS를 사용하여 테이블에 대한 작업을 수행합니다. 다음 예에서는 테이블을 연결하고 쿼리를 수행합니다.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

SAS가 쿼리 액세스만으로 생성되었기 때문에 엔터티를 삽입, 업데이트 또는 삭제하려고 하면 오류가 반환됩니다.

###액세스 제어 목록

ACL(액세스 제어 목록)을 사용하여 SAS에 액세스 정책을 설정할 수도 있습니다. 이 방법은 여러 클라이언트에서 테이블에 액세스하게 하면서 각 클라이언트에 서로 다른 액세스 정책을 제공하려는 경우에 유용합니다.

ACL은 각 정책에 ID가 연결된 액세스 정책 배열을 사용하여 구현됩니다. 다음 예제에서는 'user1'과 'user2'에 대해 하나씩 두 개의 정책을 정의합니다.

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

다음 예제에서는 **hometasks** 테이블에 대한 현재 ACL을 가져온 다음 **setTableAcl**을 사용하여 새 정책을 추가합니다. 이 접근 방식을 통해 다음을 수행할 수 있습니다.

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

ACL이 설정되고 나면 정책의 ID를 기반으로 SAS를 만들 수 있습니다. 다음 예에서는 'user2'에 대해 새 SAS를 만듭니다.

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>다음 단계

이제 테이블 저장소의 기본 사항을 살펴보았으므로 다음 링크를 통해
좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에 데이터 저장 및 액세스][]를 참조하세요.
-   [Azure 저장소 팀 블로그][](영문)를 방문하세요.
-   GitHub에서 [Azure Storage SDK for Node][] 리포지토리를 방문하세요.

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [다음 단계]: #next-steps
  [테이블 서비스 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [Node.js 응용 프로그램 만들기]: #create-app
  [저장소에 액세스하도록 응용 프로그램 구성]: #configure-access
  [Azure 저장소 연결 설정]: #setup-connection-string
  [방법: 테이블 만들기]: #create-table
  [방법: 테이블에 엔터티 추가]: #add-entity
  [방법: 엔터티 업데이트]: #update-entity
  [방법: 엔터티 그룹 작업]: #change-entities
  [방법: 엔터티 쿼리]: #query-for-entity
  [방법: 엔터티 집합 쿼리]: #query-set-entities
  [방법: 엔터티 속성 하위 집합 쿼리]: #query-entity-properties
  [방법: 엔터티 삭제]: #delete-entity
  [방법: 테이블 삭제]: #delete-table

  [OData.org]: http://www.odata.org/
  [REST API 사용]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh264518.aspx
  [Azure 관리 포털]: http://manage.windowsazure.com

  [Node.js 클라우드 서비스]: /ko-kr/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그 방문]: http://blogs.msdn.com/b/windowsazurestorage/
  [WebMatrix를 사용하는 웹 사이트]: /ko-kr/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js 클라우드 서비스 및 저장소]: /ko-kr/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Node.js 웹 응용 프로그램 및 저장소]: /ko-kr/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /ko-kr/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<!--HONumber=35_1-->
