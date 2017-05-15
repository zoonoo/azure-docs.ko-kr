---
title: "Python에서 테이블 저장소를 사용하는 방법 | Microsoft Docs"
description: "Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다."
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34d075658514045e28d6a784c579528bb3eac376
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-use-table-storage-from-python"></a>Python에서 테이블 저장소를 사용하는 방법
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>개요
이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Python으로 작성되었으며 [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)을 사용합니다. 테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리 등의 시나리오를 다룹니다.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>테이블 만들기
**TableService** 개체를 사용하면 테이블 서비스로 작업할 수 있습니다. 다음 코드는 **TableService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 위쪽에 다음 코드를 추가합니다.

```python
from azure.storage.table import TableService, Entity
```

다음 코드는 저장소 계정 이름 및 계정 키를 사용하여 **TableService** 개체를 만듭니다.  'myaccount' 및 'mykey'를 사용자의 계정 이름 및 키로 바꾸세요.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가
엔터티를 추가하려면 먼저 엔터티 속성 이름과 값을 정의하는 사전 또는 엔터티를 만듭니다. 모든 엔터티에 대해 **PartitionKey** 및 **RowKey**를 지정해야 합니다. 이러한 키는 엔터티의 고유 식별자입니다. 이러한 값을 사용하면 다른 속성보다 훨씬 빠르게 쿼리할 수 있습니다. 시스템에서는 **PartitionKey** 를 사용하여 여러 저장소 노드를 통해 테이블 엔터티를 자동으로 분산합니다.
동일한 **PartitionKey** 를 가진 엔터티는 동일한 노드에 저장됩니다. **RowKey** 는 엔터티가 속하는 파티션 내에서 엔터티의 고유한 ID입니다.

테이블에 엔터티를 추가하려면 사전 개체를 **insert\_entity** 메서드에 전달합니다.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

**Entity** 클래스의 인스턴스를 **insert\_entity** 메서드에 전달할 수도 있습니다.

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '2'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

## <a name="update-an-entity"></a>엔터티 업데이트
이 코드는 이전 버전의 기존 엔터티를 업데이트된 버전으로 바꾸는 방법을 보여 줍니다.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

업데이트 중인 엔터티가 없는 경우 업데이트 작업이 실패합니다. 엔터티가 있었는지 여부와 상관없이 엔터티를 저장하려면 **insert\_or\_replace_entity**를 사용합니다.
다음 예제에서 첫 번째 호출은 기존 엔터티를 바꿉니다. **PartitionKey** 및 **RowKey**가 지정된 엔터티가 테이블에 없으므로 두 번째 호출은 새 엔터티를 삽입합니다.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

## <a name="change-a-group-of-entities"></a>엔터티 그룹 변경
서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 그러려면 **TableBatch** 클래스를 사용합니다. 일괄 제출하려면 **commit\_batch**를 호출합니다. 일괄 변경하려면 모든 엔터티가 동일한 파티션에 있어야 합니다. 아래 예제는 두 엔터티를 일괄적으로 함께 추가합니다.

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task10)
batch.insert_entity(task11)
table_service.commit_batch('tasktable', batch)
```

컨텍스트 관리자 구문에서 Batch를 사용할 수도 있습니다.

```python
task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task12)
    batch.insert_entity(task13)
```

## <a name="query-for-an-entity"></a>엔터티 쿼리
테이블에서 엔터티를 쿼리하려면 **PartitionKey** 및 **RowKey**를 전달하여 **get\_entity** 메서드를 사용합니다.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>엔터티 집합 쿼리
이 예제에서는 **PartitionKey**를 기준으로 Seattle에서의 모든 작업을 찾습니다.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리
테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다.
*프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. **select** 매개 변수를 사용하고 가져올 속성의 이름을 클라이언트에 전달합니다.

다음 코드의 쿼리는 테이블에 있는 엔터티의 설명만 반환합니다.

> [!NOTE]
> 다음 코드 조각은 Azure Storage에 대해서만 작동하며 저장소 에뮬레이터에서 지원되지 않습니다.
>
>

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>엔터티 삭제
파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '1')
```

## <a name="delete-a-table"></a>테이블 삭제
다음 코드는 저장소 계정에서 테이블을 삭제합니다.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* [Python 개발자 센터](/develop/python/)
* [Azure 저장소 서비스 REST API](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
