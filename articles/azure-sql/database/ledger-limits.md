---
title: Azure SQL Database 원장에 대한 제한 사항
description: Azure SQL Database에서 원장 기능의 제한 사항
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 1a3451356f0c38e8006b4724873f2b82f39d6a85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388285"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Azure SQL Database 원장에 대한 제한 사항

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 **공개 미리 보기** 로 제공됩니다.

이 문서에서는 Azure SQL Database와 함께 원장 테이블을 사용할 때의 제한 사항에 대한 개요를 제공합니다.  

## <a name="limitations"></a>제한 사항

| 함수 | 제한 사항 |
| :--- | :--- |
| [원장 데이터베이스](ledger-database-ledger.md) 비활성화   | 사용하도록 설정되면 원장 데이터베이스를 비활성화할 수 없습니다. |
| 최대 열 개수 | 생성될 때 [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)은 4개의 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열을 원장 테이블에 추가하고 [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)은 원장 테이블에 두 개의 열을 추가합니다. 이러한 새 열은 Azure SQL Database(1024)에서 지원되는 최대 열 수에 대해 계산됩니다. |
| 제한되는 데이터 형식 | XML, SqlVariant, 사용자 정의 형식 및 FILESTREAM 데이터 형식은 지원되지 않습니다. |
| 메모리 내 테이블 | 메모리 내 테이블은 지원되지 않습니다. |
| 스파스 열 집합 | 스파스 열 집합은 지원되지 않습니다. |
| 원장 잘림 | [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)에서 이전 데이터 또는 [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)의 기록 테이블 삭제는 지원되지 않습니다. |
| 기존 테이블을 원장 테이블로 변환 | 원장을 사용하지 않는 데이터베이스의 기존 테이블은 원장 테이블로 변환할 수 없습니다. |
|[자동화된 다이제스트 관리](ledger-digest-management-and-database-verification.md)에 대한 LRS 지원 | [Azure Storage 변경이 불가능한 Blob](../../storage/blobs/storage-blob-immutable-storage.md)을 사용하는 원장 테이블이 포함된 자동화된 다이제스트 관리는 사용자가 [LRS(로컬 중복 스토리지)](../../storage/common/storage-redundancy.md#locally-redundant-storage) 계정을 사용할 수 있는 기능을 제공하지 않습니다.|

## <a name="remarks"></a>설명

- 원장 데이터베이스가 생성되면 데이터베이스에서 기본적으로 생성되는 모든 새 테이블(`APPEND_ONLY = ON` 절을 지정하지 않고)은 [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)이 됩니다. [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)은 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 명령문을 사용하여 만들 수 있습니다.
- 원장 테이블은 FILETABLE일 수 없습니다.
- 원장 테이블에는 전체 텍스트 인덱스를 사용할 수 없습니다.
- 원장 테이블의 이름을 바꿀 수 없습니다.
- 원장 테이블은 다른 스키마로 이동할 수 없습니다.
- null 허용 열만 WITH VALUES로 지정되지 않은 경우 원장 테이블에 추가할 수 있습니다.
- 원장 테이블의 열은 삭제할 수 없습니다.
- 원장 테이블에는 결정적 계산 열만 허용됩니다.
- 기존 열은 이 열의 형식을 수정하는 방식으로 변경할 수 없습니다.
  - 다음을 변경할 수 있습니다.
    - Null 허용 여부
    - nvarchar/ntext 열에 대한 데이터 정렬 및 char/텍스트 열에 대한 코드 페이지가 변경되지 않는 경우
    - 가변 길이 열의 길이 변경
    - 스파스
- SWITCH IN/OUT은 원장 테이블에 허용되지 않습니다.
- LTR(장기 백업)은 `LEDGER = ON`이 있는 데이터베이스에 대해 지원되지 않습니다.
- `LEDGER` 또는 `SYSTEM_VERSIONING`은 원장 테이블에 대해 사용하지 않도록 설정될 수 없습니다.
- `UPDATETEXT` 및 `WRITETEXT` API는 원장 테이블에서 사용할 수 없습니다.
- 트랜잭션은 최대 200개 원장 테이블을 업데이트할 수 있습니다.
- 업데이트 가능한 원장 테이블의 경우 temporal 테이블의 모든 제한 사항들을 상속합니다.
- 원장 테이블에서는 변경 추적이 허용되지 않습니다.
- 클러스터형 Columnstore 인덱스가 있는 경우 원장 테이블에는 rowstore 비클러스터형 인덱스가 있을 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)   
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)   
- [데이터베이스 원장](ledger-database-ledger.md)   
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)   
