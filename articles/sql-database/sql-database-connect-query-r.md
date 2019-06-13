---
title: R을 사용하여 Azure SQL Database 쿼리
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 문서에서는 R 스크립트를 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터베이스를 쿼리하는 방법을 보여줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416371"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>빠른 시작: R을 사용하여 Azure SQL 데이터베이스 쿼리(미리 보기)

 이 빠른 시작에서는 Machine Learning Services에서 [R](https://www.r-project.org/)을 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여줍니다. Machine Learning Services는 Azure SQL Database의 기능이며, 데이터베이스 내 R 스크립트를 실행하는 데 사용됩니다. 자세한 내용은 [Azure SQL Database Machine Learning Services(R 포함)(미리 보기)](sql-database-machine-learning-services-overview.md)를 참조하세요.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || 단일 데이터베이스 |
  |:--- |:--- |
  | 생성| [포털](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md) |
  | 데이터 로드 | Adventure Works(빠른 시작마다 로드됨) |
  |||

  > [!NOTE]
  > Azure SQL Database Machine Learning Services(R 포함) 미리 보기 중에는 관리형 인스턴스 배포 옵션이 지원되지 않습니다.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services(R 포함)를 사용하도록 설정. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms). 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

## <a name="create-code-to-query-your-sql-database"></a>SQL 데이터베이스를 쿼리할 코드 만들기

1. **SQL Server Management Studio**를 열고 SQL 데이터베이스에 연결합니다.

   연결에 도움이 필요한 경우 [빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.

1. 전체 R 스크립트를 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 저장 프로시저에 전달합니다.

   이 스크립트는 `@script` 인수를 통해 전달됩니다. `@script` 인수 내부의 모든 항목이 유효한 R 코드여야 합니다.
   
   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > 오류가 발생하면 SQL 데이터베이스에 Machine Learning Services(R 포함) 공개 미리 보기를 사용하도록 설정되지 않은 것입니다. 위의 [필수 조건](#prerequisites)을 참조하세요.

## <a name="run-the-code"></a>코드 실행

1. [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 저장 프로시저를 실행합니다.

1. 상위 20개 범주/제품 행이 **메시지** 창에 반환되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL 데이터베이스 디자인](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services(R 포함)](sql-database-machine-learning-services-overview.md)
- [Azure SQL Database Machine Learning Services(미리 보기)에서 간단한 R 스크립트 만들기 및 실행](sql-database-quickstart-r-create-script.md)
- [Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성](sql-database-machine-learning-services-functions.md)
