---
title: "Python에서 Azure Table Storage를 사용하는 방법 | Microsoft Docs"
description: "Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/16/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0c46f04786ba4b62bd7ca22c5e25643123e6e136
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="how-to-use-table-storage-in-python"></a>Python에서 테이블 저장소를 사용하는 방법

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

이 가이드에서는 [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)을 사용하여 Python에서 일반적인 Azure Table Storage 시나리오를 수행하는 방법을 보여 줍니다. 테이블 만들기 및 삭제, 엔터티 삽입 및 쿼리 등의 시나리오를 다룹니다.

이 자습서의 시나리오를 진행하면서 [Azure Storage SDK for Python 참조](https://azure-storage.readthedocs.io/en/latest/index.html)를 참조할 수 있습니다.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python 설치

저장소 계정을 만들었으면 다음 단계는 [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)을 설치하는 것입니다. SDK 설치에 대한 자세한 내용은 GitHub의 Storage SDK for Python 리포지토리에서 [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) 파일을 참조하세요.

## <a name="create-a-table"></a>테이블 만들기

Python에서 Azure Table service를 사용하려면 [TableService][py_TableService] 모듈을 가져와야 합니다. Table 엔터티로 작업하게 되므로 [Entity][py_Entity] 클래스도 필요합니다. 둘 다 가져오려면 Python 파일 위쪽에 다음 코드를 추가합니다.

```python
from azure.storage.table import TableService, Entity
```

저장소 계정 이름 및 계정 키를 제공하여 [TableService][py_TableService] 개체를 만듭니다. `myaccount` 및 `mykey`를 계정 이름 및 키로 바꾸고 [create_table][py_create_table]을 호출하여 Azure Storage에 테이블을 만듭니다.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

엔터티를 추가하려면 먼저 엔터티를 나타내는 개체를 만든 후 [TableService][py_TableService].[insert_entity][py_insert_entity] 메서드에 전달합니다. 엔터티 개체는 [Entity][py_Entity] 형식의 사전 또는 개체일 수 있으며 엔터티의 속성 이름 및 값을 정의합니다. 모든 엔터티에는 사용자가 엔터티에 정의하는 다른 속성 외에 필수 [PartitionKey 및 RowKey](#partitionkey-and-rowkey) 속성이 있어야 합니다.

이 예제에서는 엔터티를 나타내는 사전 개체를 만든 후 [insert_entity][py_insert_entity] 메서드에 전달하여 테이블에 추가합니다.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

이 예제에서는 [Entity][py_Entity] 개체를 만든 후 [insert_entity][py_insert_entity] 메서드에 전달하여 테이블에 추가합니다.

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey 및 RowKey

모든 엔터티에 대해 **PartitionKey** 및 **RowKey** 속성을 둘 다 지정해야 합니다. 이러한 속성이 함께 모여 엔터티의 기본 키를 형성하므로 엔터티의 고유 식별자에 해당합니다. 이러한 속성만 인덱싱되므로 다른 엔터티 속성보다 이러한 값을 사용하면 훨씬 더 빠르게 쿼리할 수 있습니다.

Table service에서는 **PartitionKey**를 사용하여 저장소 노드에서 테이블 엔터티를 지능적으로 분산합니다. 동일한 **PartitionKey**를 가진 엔터티는 동일한 노드에 저장됩니다. **RowKey** 는 엔터티가 속하는 파티션 내에서 엔터티의 고유한 ID입니다.

## <a name="update-an-entity"></a>엔터티 업데이트

모든 엔터티의 속성 값을 업데이트하려면 [update_entity][py_update_entity] 메서드를 호출합니다. 이 예제에서는 기존 엔터티를 업데이트된 버전으로 바꾸는 방법을 보여 줍니다.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

업데이트 중인 엔터티가 아직 없는 경우 업데이트 작업이 실패합니다. 존재 여부에 관계 없이 엔터티를 저장하려는 경우 [insert_or_replace_entity][py_insert_or_replace_entity]를 사용합니다. 다음 예제에서 첫 번째 호출은 기존 엔터티를 바꿉니다. PartitionKey 및 RowKey가 지정된 엔터티가 테이블에 없으므로 두 번째 호출은 새 엔터티를 삽입합니다.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [update_entity][py_update_entity] 메서드는 기존 엔터티의 모든 속성 및 값을 대체합니다. 기존 엔터티에서 속성을 제거할 때도 이 메서드를 사용할 수 있습니다. [merge_entity][py_merge_entity] 메서드를 사용하여 엔터티를 완전히 바꾸지는 않으면서 기존 엔터티를 새 속성 값이나 수정된 속성 값으로 업데이트할 수 있습니다.

## <a name="modify-multiple-entities"></a>여러 엔터티 수정

Table service의 요청의 원자성 처리를 보장하기 위해 여러 작업을 일괄로 제출할 수 있습니다. 먼저 [TableBatch][py_TableBatch] 클래스를 사용하여 여러 작업을 단일 배치에 추가합니다. 그런 다음 [TableService][py_TableService].[commit_batch][py_commit_batch]를 호출하여 작업을 원자성 작업으로 제출합니다. 일괄로 수정할 모든 엔터티는 동일한 파티션에 있어야 합니다.

다음 예제에서는 두 엔터티를 일괄적으로 함께 추가합니다.

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

컨텍스트 관리자 구문에서 Batch를 사용할 수도 있습니다.

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>엔터티 쿼리

테이블의 엔터티를 쿼리하려면 PartitionKey 및 RowKey를 [TableService][py_TableService].[ get_entity][py_get_entity] 메서드에 전달합니다.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>엔터티 집합 쿼리

필터 문자열에 **filter** 매개 변수를 제공하여 엔터티 집합을 쿼리할 수 있습니다. 이 예제에서는 PartitionKey에 필터를 적용하여 Seattle에서의 모든 작업을 찾습니다.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리

쿼리에서 각 엔터티에 대해 반환되는 속성을 제한할 수도 있습니다. *프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티 또는 결과 집합에 대한 쿼리 성능을 향상시킬 수 있습니다. **select** 매개 변수를 사용하고 반환하려는 가져올 속성의 이름을 클라이언트에 전달합니다.

다음 코드의 쿼리는 테이블에 있는 엔터티의 설명만 반환합니다.

> [!NOTE]
> 다음 코드 조각은 Azure Storage에 대해서만 작동하며 저장소 에뮬레이터에서 지원되지 않습니다.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>엔터티 삭제

PartitionKey 및 RowKey를 [delete_entity][py_delete_entity] 메서드에 제공하여 엔터티를 삭제합니다.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>테이블 삭제

테이블 또는 해당 엔터티가 더 이상 필요하지 않으면 [delete_table][py_delete_table] 메서드를 호출하여 Azure Storage에서 테이블을 영구적으로 삭제합니다.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>다음 단계

* [ 참조](https://azure-storage.readthedocs.io/en/latest/index.html)
* [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
* [Python 개발자 센터](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md): Windows, macOS, 및 Linux에서 Azure Storage 데이터를 시각적으로 사용하기 위한 플랫폼 간 무료 응용 프로그램입니다.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch

