---
title: 원장 테이블을 확인하여 변조를 감지하는 방법
description: 이 문서에서는 Azure SQL Database 테이블의 변조 여부를 확인하는 방법을 설명합니다.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 05/25/2021
ms.openlocfilehash: 6669b071d79c78b35dc2cfb94f99fa9597f44bd6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388237"
---
# <a name="how-to-verify-a-ledger-table-to-detect-tampering"></a>원장 테이블을 확인하여 변조를 감지하는 방법

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 **공개 미리 보기** 로 제공됩니다.

이 문서에서는 Azure SQL Database 원장 테이블에 있는 데이터의 무결성을 확인합니다. [Azure SQL Database를 만들 때](ledger-create-a-single-database-with-ledger-enabled.md) **자동 다이제스트 스토리지 사용** 을 선택한 경우 Azure Portal 지침을 따라 [쿼리 편집기](connect-query-portal.md)에서 데이터베이스 원장을 확인하는 데 필요한 Transact-SQL(T-SQL) 스크립트를 자동으로 생성해야 합니다. [SQL Server Management Studio(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)나 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 T-SQL 지침을 따라도 됩니다.

## <a name="prerequisite"></a>필수 요소

- 활성화된 Azure 구독. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- [원장이 활성화된 Azure SQL Database를 만듭니다.](ledger-create-a-single-database-with-ledger-enabled.md)
- [업데이트 가능한 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md) 또는 [추가 전용 원장 테이블 만들기 및 사용](ledger-how-to-append-only-ledger-tables.md)

## <a name="run-ledger-verification-for-azure-sql-database"></a>Azure SQL Database를 대상으로 원장 확인 실행

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)을 열고 **모든 리소스** 를 선택한 다음 확인할 데이터베이스를 찾습니다. 이 SQL 데이터베이스를 선택합니다.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="모든 리소스 탭을 표시하는 Azure Portal":::

1. **보안** 에서 **원장** 옵션을 선택합니다.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Azure Portal 원장 보안 탭":::

1. **원장** 창에서 **</> 데이터베이스 확인** 단추를 선택하고 창에서 미리 채워진 텍스트의 **복사** 아이콘을 선택합니다.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure Portal 데이터베이스 확인 단추":::

1. 왼쪽 메뉴에서 **쿼리 편집기** 를 엽니다.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Azure Portal 쿼리 편집기 단추":::

1. **쿼리 편집기** 에서 3단계에서 복사한 T-SQL 스크립트를 붙여넣고 **실행** 을 선택합니다.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="데이터베이스를 확인하기 위해 쿼리 편집기를 실행하는 Azure Portal":::

1. 확인에 성공하면 **결과** 창에 다음이 반환됩니다.

   - 데이터베이스에 변조가 없는 경우 다음과 같은 메시지가 표시됩니다.

   ```output
   Ledger verification successful
   ```

   - 데이터베이스에 변조가 있는 경우 **메시지** 창에 다음 오류가 발생합니다.
  
   ```output
   Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
   ```

# <a name="t-sql"></a>[T-SQL](#tab/t-sql)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 데이터베이스에 연결합니다.
1. 다음 T-SQL 문을 사용하여 새 쿼리를 만듭니다.

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. 쿼리 실행. 결과에는 최신 데이터베이스 다이제스트가 포함되며 현재 시점의 데이터베이스 해시가 표시됩니다. 다음 단계에서 사용할 결과의 내용을 복사합니다.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Azure Data Studio를 사용하여 다이제스트 결과 검색":::

1. 다음 T-SQL 문을 사용하여 새 쿼리를 만듭니다. `<YOUR DATABASE DIGEST>`를 이전 단계에서 복사한 이름으로 바꿉니다.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. 쿼리 실행. **메시지** 에 다음과 같은 성공 메시지가 포함됩니다.  

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Azure Data Studio를 사용하여 원장 확인용 T-SQL 쿼리를 실행한 후의 메시지":::

   > [!TIP]
   > 최신 다이제스트를 사용하여 원장 확인을 실행하면 다이제스트가 생성된 시점부터 확인이 실행될 때까지의 데이터베이스만 확인됩니다. 데이터베이스의 기록 데이터가 변조되지 않았는지 확인하려면 여러 데이터베이스 다이제스트 파일을 사용하여 확인을 실행하세요. 데이터베이스를 확인하려는 시점부터 시작해야 합니다. 여러 다이제스트를 전달하는 확인은 아래 쿼리와 비슷하게 보입니다.

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 원장 개요](ledger-overview.md)
- [데이터베이스 원장](ledger-database-ledger.md)
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)
- [ACL(Azure Confidential Ledger)에 저장된 다이제스트에 액세스하는 방법](ledger-how-to-access-acl-digest.md)
