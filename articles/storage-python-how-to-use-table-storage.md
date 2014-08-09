<properties linkid="develop-python-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Learn how to use the Table service from Python to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="" solutions="" manager="" editor="" />

Python에서 테이블 저장소 서비스를 사용하는 방법
===============================================

이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python API를 사용하여 작성되었습니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리** 등의 시나리오를 다룹니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

[테이블 서비스 정의](#what-is)   
 [개념](#concepts)   
 [Azure 저장소 계정 만들기](#create-account)   
 [방법: 테이블 만들기](#create-table)   
 [방법: 테이블에 엔터티 추가](#add-entity)   
 [방법: 엔터티 업데이트](#update-entity)   
 [방법: 엔터티 그룹 변경](#change-entities)   
 [방법: 엔터티 쿼리](#query-for-entity)   
 [방법: 엔터티 집합 쿼리](#query-set-entities)   
 [방법: 엔터티 속성 하위 집합 쿼리](#query-entity-properties)   
 [방법: 엔터티 삭제](#delete-entity)   
 [방법: 테이블 삭제](#delete-table)   
 [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Azure 저장소 계정 만들기
------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**참고:** Python 또는 클라이언트 라이브러리를 설치해야 하는 경우 [Python 설치 가이드](../python-how-to-install/)(영문)를 참조하십시오.

테이블을 만드는 방법
--------------------

**TableService** 개체를 사용하면 테이블 서비스로 작업할 수 있습니다. 다음 코드는 **TableService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

    from azure.storage import *

다음 코드는 저장소 계정 이름 및 계정 키를 사용하여 **TableService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 실제 계정 및 키로 바꾸십시오.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

테이블에 엔터티를 추가하는 방법
-------------------------------

엔터티를 추가하려면 먼저 엔터티 속성 이름과 값을 정의하는 사전을 만듭니다. 모든 엔터티에 대해 **PartitionKey** 및 **RowKey**를 지정해야 합니다. 이 두 키는 엔터티의 고유한 식별자이며, 다른 속성보다 훨씬 더 빠르게 쿼리할 수 있는 값입니다. 시스템에서는 **PartitionKey**를 사용하여 여러 저장소 노드를 통해 테이블 엔터티를 자동으로 분산합니다. **PartitionKey**가 동일한 엔터티는 동일한 노드에 저장됩니다. **RowKey**는 엔터티가 속하는 파티션 내에서 엔터티의 고유한 ID입니다.

테이블에 엔터티를 추가하려면 사전 개체를 **insert\_entity** 메서드에 전달합니다.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

**Entity** 클래스의 인스턴스를 **insert\_entity** 메서드에 전달할 수도 있습니다.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

엔터티를 업데이트하는 방법
--------------------------

이 코드는 이전 버전의 기존 엔터티를 업데이트된 버전으로 바꾸는 방법을 보여 줍니다.

    task = {'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

업데이트 중인 엔터티가 없는 경우 업데이트 작업이 실패합니다. 엔터티가 있었는지 여부와 상관없이 엔터티를 저장하려면 **insert\_or\_replace\_entity**를 사용합니다. 다음 예제에서 첫 번째 호출은 기존 엔터티를 바꿉니다. **PartitionKey**와 **RowKey**가 지정된 엔터티가 테이블에 없으므로 두 번째 호출은 새 엔터티를 삽입합니다.

    task = {'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

엔터티 그룹을 변경하는 방법
---------------------------

서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 이렇게 하려면 **TableService**에서 **begin\_batch** 메서드를 사용한 다음 평상시처럼 여러 작업을 호출합니다. 일괄 제출하려면 **commit\_batch**를 호출합니다. 일괄 변경하려면 모든 엔터티가 동일한 파티션에 있어야 합니다. 아래 예제는 두 엔터티를 일괄적으로 함께 추가합니다.

    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

엔터티를 쿼리하는 방법
----------------------

테이블에서 엔터티를 쿼리하려면 **PartitionKey** 및 **RowKey**를 전달하여 **get\_entity** 메서드를 사용합니다.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

엔터티 집합을 쿼리하는 방법
---------------------------

이 예제에서는 **PartitionKey**를 기반으로 Seattle에서의 모든 작업을 찾습니다.

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

엔터티 속성 하위 집합을 쿼리하는 방법
-------------------------------------

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. *프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. **select** 매개 변수를 사용하고 가져올 속성의 이름을 클라이언트에 전달합니다.

다음 코드의 쿼리는 테이블에 있는 엔터티의 **Descriptions**만 반환합니다.

*다음 코드 조작은 클라우드 저장소 서비스에 대해서만 작동하며 저장소 에뮬레이터에서 지원되지 않습니다.*

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
    for task in tasks:
        print(task.description)

엔터티를 삭제하는 방법
----------------------

파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

테이블을 삭제하는 방법
----------------------

다음 코드에서는 저장소 계정에서 테이블을 삭제합니다.

    table_service.delete_table('tasktable')

다음 단계
---------

이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   다음 MSDN 참조를 확인하십시오. [Azure에서 데이터 저장 및 액세스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx)
-   [Azure 저장소 팀 블로그(영문)를 방문하십시오.](http://blogs.msdn.com/b/windowsazurestorage/)

