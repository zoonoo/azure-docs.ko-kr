---
title: Key differences for Machine Learning Services (preview)
description: 이 항목에서는 Azure SQL Database Machine Learning Services(R 포함)와 SQL Server Machine Learning 서비스의 주요 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 533e2b9e50a92cce1419da521d8cebc4955e4df6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462127"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Key differences between Machine Learning Services in Azure SQL Database (preview) and SQL Server

The functionality of Azure SQL Database Machine Learning Services (with R) in  (preview) is similar to [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Below are some key differences.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>언어 지원

SQL Server는 [확장성 프레임워크](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)를 통해 R 및 Python을 지원합니다. SQL Database는 두 언어를 모두 지원하지 않습니다. 주요 차이점은 다음과 같습니다.

- R은 SQL Database에서 유일하게 지원되는 언어입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
- R 버전은 3.4.4입니다.
- `sp_configure`를 통해 `external scripts enabled`를 구성할 필요가 없습니다. [등록](sql-database-machine-learning-services-overview.md#signup)하면 SQL 데이터베이스에 대한 기계 학습을 사용할 수 있습니다.

## <a name="package-management"></a>패키지 관리

R 패키지 관리와 설치 작업은 SQL Database와 SQL Server 간에 다릅니다. 차이점은 다음과 같습니다.

- R 패키지는 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 또는 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)를 통해 설치됩니다.
- 패키지는 아웃바운드 네트워크 호출을 수행할 수 없습니다. This limitation is similar to the [default firewall rules for Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, but can't be changed in SQL Database.
- 설치나 사용을 위해 OS API에 대한 액세스가 필요하거나 외부 런타임(예: Java)에 의존하는 패키지는 지원되지 않습니다.

## <a name="writing-to-a-temporary-table"></a>Writing to a temporary table

If you're using RODBC in Azure SQL Database, then you can't write to a temporary table, whether it's created inside or outside of the `sp_execute_external_script` session. The workaround is to use [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) and [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite=FALSE and append="rows") to write to a global temporary table created before the `sp_execute_external_script` query.

## <a name="resource-governance"></a>리소스 관리

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 및 외부 리소스 풀을 통해 R 리소스를 제한할 수 없습니다.

During the public preview, R resources are set to a maximum of 20% of the SQL Database resources, and depend on which service tier you choose. 자세한 내용은 [Azure SQL Database 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 참조하세요.
### <a name="insufficient-memory-error"></a>Insufficient memory error

If there is insufficient memory available for R, you will get an error message. Common error messages are:

- Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime
- 'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"
- An external script error occurred: Error: cannot allocate vector of size.

Memory usage depends on how much is used in your R scripts and the number of parallel queries being executed. If you receive the errors above, you can scale your database to a higher service tier to resolve this.

## <a name="next-steps"></a>다음 단계

- See the overview, [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
