<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

Node.js에서 테이블 서비스를 사용하는 방법
=========================================

이 가이드에서는 Azure 테이블 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리** 등의 시나리오를 다룹니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [테이블 서비스 정의](#what-is)
-   [개념](#concepts)
-   [Azure 저장소 계정 만들기](#create-account)
-   [Node.js 응용 프로그램 만들기](#create-app)
-   [저장소에 액세스하도록 응용 프로그램 구성](#configure-access)
-   [Azure 저장소 연결 설정](#setup-connection-string)
-   [방법: 테이블 만들기](#create-table)
-   [방법: 테이블에 엔터티 추가](#add-entity)
-   [방법: 엔터티 업데이트](#update-entity)
-   [방법: 엔터티 그룹 작업](#change-entities)
-   [방법: 엔터티 쿼리](#query-for-entity)
-   [방법: 엔터티 집합 쿼리](#query-set-entities)
-   [방법: 엔터티 속성 하위 집합 쿼리](#query-entity-properties)
-   [방법: 엔터티 삭제](#delete-entity)
-   [방법: 테이블 삭제](#delete-table)
-   [다음 단계](#next-steps)

테이블 서비스 정의
------------------

Azure 테이블 서비스는 다량의 구조화된 데이터를 저장합니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락합니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다. 테이블 서비스의 일반적인 사용은 다음과 같습니다.

-   처리량 요구를 충족하도록 자동으로 크기 조정되는 다량의 구조화된 데이터(수 TB) 저장
-   복합 조인, 외래 키 또는 저장 프로시저가 필요하지 않고 빠른 액세스를 위해 역정규화할 수 있는 데이터 집합 저장
-   클러스터형 인덱스를 사용하여 사용자 프로필과 같은 데이터를 빠르게 쿼리

테이블 서비스를 사용하여 구조화된 비관계형 데이터 집합을 저장 및 쿼리할 수 있으며, 볼륨이 증가하면 테이블 규모가 확장됩니다.

개념
----

테이블 서비스에는 다음 구성 요소가 포함됩니다.

![Table1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **URL 형식:** 코드에서 다음 주소 형식을 사용하여 계정의 테이블 주소를 지정합니다.

        http://storageaccount.table.core.windows.net/table  

    OData 프로토콜과 함께 이 주소를 사용하여 Azure 테이블 주소를 직접 지정할 수 있습니다. 자세한 내용은 [OData.org](http://www.odata.org/)를 참조하십시오.

-   **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 저장소 계정에 있는 Blob, 테이블 및 큐 콘텐츠의 총 크기는 100TB를 초과할 수 없습니다.

-   **테이블**: 테이블은 무제한 엔터티 컬렉션입니다. 테이블은 엔터티에 스키마를 적용하지 않으므로 단일 테이블에 각기 다른 속성 집합을 가진 엔터티가 포함될 수 있습니다. 한 계정에 많은 테이블이 포함될 수 있습니다.

-   **엔터티**: 엔터티는 데이터베이스 행과 유사한 속성 집합입니다. 엔터티의 크기는 최대 1MB일 수 있습니다.

-   **속성**: 속성은 이름 값 쌍입니다. 각 엔터티에 데이터를 저장할 속성을 최대 252개까지 포함할 수 있습니다. 또한 각 엔터티에는 파티션 키, 행 키 및 타임스탬프를 지정하는 시스템 속성 세 개가 있습니다. 동일한 파티션 키를 가진 엔터티는 보다 신속하게 쿼리할 수 있으며 원자성 작업으로 삽입/업데이트할 수 있습니다. 엔터티의 행 키는 파티션 내의 고유 식별자입니다.

Azure 저장소 계정 만들기
------------------------

저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다. [REST API를 사용](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh264518.aspx)하여 저장소 계정을 만들 수도 있습니다.

1.  [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2.  탐색 창 맨 아래쪽에서 **+새로 만들기**를 클릭합니다.

    ![+새로 만들기](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.

    ![빠른 생성 대화 상자](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.

5.  저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 저장소를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

6.  **저장소 계정 만들기**를 클릭합니다.

Node.js 응용 프로그램 만들기
----------------------------

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js 클라우드 서비스]({localLink:2221} "Web App with Express")(Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)를 참조하십시오.

저장소에 액세스하도록 응용 프로그램 구성
----------------------------------------

Azure 저장소를 사용하려면 저장소 REST 라이브러리와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js Azure 패키지를 다운로드하여 사용해야 합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure**를 입력합니다. 그러면 다음과 같이 출력됩니다.

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 저장소에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure** 패키지를 찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의 **server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure');

Azure 저장소 연결 설정
----------------------

Azure 모듈은 AZURE\_STORAGE\_ACCOUNT 및 AZURE\_STORAGE\_ACCESS\_KEY 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되어 있지 않은 경우 **TableService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 클라우드 서비스의 구성 파일에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 클라우드 서비스 및 저장소](/en-us/develop/nodejs/tutorials/web-app-with-storage/)를 참조하십시오.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 웹 응용 프로그램 및 저장소](/en-us/develop/nodejs/tutorials/web-site-with-storage/)를 참조하십시오.

테이블을 만드는 방법
--------------------

다음 코드는 **TableService** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다. **server.js**의 위쪽에 다음을 추가합니다.

    var tableService = azure.createTableService();

**createTableIfNotExists**를 호출하면 지정된 테이블(있는 경우)이 반환되거나 새 테이블이 지정된 이름으로 만들어집니다(테이블이 없는 경우). 다음 예제에서는 'mytable'이라는 새 테이블(없는 경우)을 만듭니다.

    tableService.createTableIfNotExists('mytable', function(error){
        if (!error) {
            // Table exists or created
        }
    });

### 필터

**TableService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

     function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

     function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음은 **ExponentialRetryPolicyFilter**를 사용하는 **TableService** 개체를 만듭니다.

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

테이블에 엔터티를 추가하는 방법
-------------------------------

엔터티를 추가하려면 먼저 엔터티 속성과 데이터 형식을 정의하는 개체를 만듭니다. 모든 엔터티에 대해 **PartitionKey** 및 **RowKey**를 지정해야 합니다. 이 두 키는 엔터티의 고유한 식별자이며, 다른 속성보다 훨씬 더 빠르게 쿼리할 수 있는 값입니다. 시스템에서는 **PartitionKey**를 사용하여 여러 저장소 노드를 통해 테이블의 엔터티를 자동으로 분산합니다. **PartitionKey**가 동일한 엔터티는 동일한 노드에 저장됩니다. **RowKey**는 엔터티가 속하는 파티션 내에서 엔터티의 고유한 ID입니다. 테이블에 엔터티를 추가하려면 엔터티 개체를 **insertEntity** 메서드에 전달합니다.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Take out the trash'
        , DueDate: new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if (!error) {
            // Entity inserted
        }
    });

엔터티를 업데이트하는 방법
--------------------------

다음과 같은 여러 메서드를 사용하여 기존 엔터티를 업데이트할 수 있습니다.

-   **updateEntity** - 기존 엔터티를 바꾸어서 업데이트합니다.

-   **mergeEntity** - 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다.

-   **insertOrReplaceEntity** - 기존 엔터티를 바꾸어서 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

-   **insertOrMergeEntity** - 새 속성 값을 기존 항목에 병합하여 기존 엔터티를 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

다음 예제에서는 **updateEntity**를 사용하여 엔터티를 업데이트하는 방법을 보여 줍니다.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if (!error) {
            // Entity has been updated
        }
    });

**updateEntity** 및 **mergeEntity**를 사용할 때 업데이트 중인 엔터티가 없는 경우 업데이트 작업이 실패합니다. 따라서 엔터티의 존재 여부에 상관없이 엔터티를 저장하려면 **insertOrReplaceEntity** 또는 **insertOrMergeEntity**를 대신 사용해야 합니다.

엔터티 그룹 작업 방법
---------------------

서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 이렇게 하려면 **TableService**에서 **beginBatch** 메서드를 사용한 다음 평상시처럼 여러 작업을 호출합니다. 이러한 연산자의 콜백 함수가 작업을 서버에 제출하지 않고 일괄적으로 처리하도록 지정한다는 점만 다릅니다. 일괄 제출하려면 **commitBatch**를 호출합니다. 이 메서드에 제공되는 콜백은 전체 배치가 성공적으로 제출되었는지 여부를 나타냅니다. 다음 예제에서는 두 엔터티를 일괄적으로 제출하는 방법을 보여 줍니다.

    var tasks=[
        {
            PartitionKey : 'hometasks'
            , RowKey : '1'
            , Description : 'Take out the trash.'
            , DueDate : new Date(2012, 6, 20)
        }
        , {
            PartitionKey : 'hometasks'
            , RowKey : '2'
            , Description : 'Wash the dishes.'
            , DueDate : new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if (!error) {
                    // Entity inserted
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if (!error) {
                // All inserts completed
                tableService.commitBatch(function(error){
                    if (!error) {
                        // Batch successfully commited
                    }
                });
            }
        });

**참고**

위 예제에서는 'async' 모듈을 사용하여 **commitBatch**를 호출하기 전에 모든 엔터티를 제출하도록 합니다.

엔터티를 쿼리하는 방법
----------------------

테이블에서 엔터티를 쿼리하려면 **PartitionKey** 및 **RowKey**를 전달하여 **queryEntity** 메서드를 사용합니다.

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if (!error) {
                // entity contains the returned entity
            }
        });

엔터티 집합을 쿼리하는 방법
---------------------------

테이블을 쿼리하려면 **TableQuery** 개체를 사용하여 **select**, **from**, **where**(**wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey**, **whereNextRowKey** 등과 같은 편의 절 포함), **and**, **or** 및 **top** 절을 사용하여 쿼리 식을 작성합니다. 그런 다음 쿼리 식을 **queryEntities** 메서드에 전달합니다. 콜백 내의 **for** 루프에서 결과를 사용할 수 있습니다.

이 예제에서는 **PartitionKey**를 기반으로 Seattle에서의 모든 작업을 찾습니다.

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //entities contains an array of entities
        }
    });

엔터티 속성 하위 집합을 쿼리하는 방법
-------------------------------------

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. *프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. **select** 절을 사용하고 가져올 속성의 이름을 클라이언트에 전달합니다.

다음 코드의 쿼리에서는 테이블에서 엔터티에 대한 **설명**을 반환합니다. 프로그램 출력에서 **DueDate**는 서버에서 전송되지 않았기 때문에 **undefined**로 표시됩니다.

**참고**

다음 코드 조작은 클라우드 저장소 서비스에 대해서만 작동합니다. **select** 키워드는 저장소 에뮬레이터에서 지원되지 않습니다.

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //entities contains an array of entities
        }
    });

엔터티를 삭제하는 방법
----------------------

파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다. 이 예제에서 **task1** 개체는 삭제할 엔터티의 **RowKey** 및 **PartitionKey** 값을 포함합니다. 그런 다음 개체는 **deleteEntity** 메서드에 전달됩니다.

    tableService.deleteEntity('mytable'
        , {
            PartitionKey : 'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if (!error) {
                // Entity deleted
            }
        });

테이블을 삭제하는 방법
----------------------

다음 코드에서는 저장소 계정에서 테이블을 삭제합니다.

    tableService.deleteTable('mytable', function(error){
        if (!error) {
            // Table deleted
        }
    });

다음 단계
---------

이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   다음 MSDN 참조를 확인하십시오. [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx)
-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.
-   GitHub에서 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) 리포지토리를 방문하십시오.

