---
title: Machine Learning Services와 함께 R을 사용하여 데이터베이스 쿼리(미리 보기)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 문서에서는 Azure SQL Database Machine Learning Services와 함께 R 스크립트를 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 쿼리하는 방법을 보여줍니다.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 23dc784586e85b8cbdc816ac5f4d54556fc1ffb7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456994"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>빠른 시작: Machine Learning Services와 함께 R을 사용하여 Azure SQL 데이터베이스 쿼리(미리 보기)

이 빠른 시작에서는 Machine Learning Services와 함께 R을 사용하여 Azure SQL 데이터베이스에 연결하고, T-SQL 문을 사용하여 데이터를 쿼리합니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [평가판 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL 데이터베이스](sql-database-single-database-get-started.md)
- R이 활성화된 [Machine Learning Services](sql-database-machine-learning-services-overview.md).
- [SSMS(SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

R이 포함된 Machine Learning Services는 데이터베이스 내 R 스크립트를 실행하는 데 사용되는 Azure SQL Database의 기능입니다. 자세한 내용은 [R 프로젝트](https://www.r-project.org/)를 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

## <a name="create-code-to-query-your-sql-database"></a>SQL 데이터베이스를 쿼리할 코드 만들기

1. **SQL Server Management Studio**를 열고 SQL 데이터베이스에 연결합니다.

   연결에 도움이 필요한 경우 [빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.

1. 전체 R 스크립트를 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 저장 프로시저에 전달합니다.

   스크립트는 `@script` 인수를 통해 전달됩니다. `@script` 인수 안의 모든 항목이 유효한 R 코드여야 합니다.
   
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
