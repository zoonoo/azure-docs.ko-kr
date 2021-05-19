---
title: Azure SQL 리소스를 만들거나 수정하는 블록 T-SQL 명령
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: 이 문서에서는 Azure 관리자가 T-SQL 명령을 차단하여 Azure SQL 리소스를 만들거나 수정할 수 있는 기능에 대해 자세히 설명합니다.
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 65ea6d78eb807671df1732392e112d368877a2b5
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132056"
---
# <a name="what-is-block-t-sql-crud-feature"></a>블록 T-SQL CRUD 기능이란?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


이 기능을 사용하면 Azure 관리자는 T-SQL을 통해 Azure SQL 리소스의 생성 또는 수정을 차단할 수 있습니다. 이는 Azure SQL 데이터베이스 또는 관리형 인스턴스의 SQL 리소스에 영향을 주지 않도록 T-SQL 명령이 차단되도록 구독 수준에서 적용됩니다.

## <a name="overview"></a>개요

T-SQL을 통한 리소스 생성 또는 수정을 차단하고 지정된 구독에 대해 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 리소스 관리를 적용하려면 Azure Portal의 구독 수준 미리 보기 기능을 사용하면 됩니다. 이 기능은 [Azure 정책](../../governance/policy/overview.md)을 사용하여 ARM 템플릿을 통해 조직 표준을 적용하는 경우에 특히 유용합니다. T-SQL은 Azure 정책을 준수하지 않으므로 T-SQL 생성 또는 수정 작업에 대한 블록을 적용할 수 있습니다. 차단된 구문에는 Azure SQL의 데이터베이스, 특히 `CREATE DATABASE`, `ALTER DATABASE` 및 `DROP DATABASE` 문에 대한 CRUD(만들기, 업데이트, 삭제) 문이 포함됩니다. 

T-SQL CRUD 작업은 Azure Portal, [PowerShell](/powershell/module/az.resources/register-azproviderfeature) 또는 [Azure CLI](/cli/azure/feature#az_feature_register)를 통해 차단할 수 있습니다.

## <a name="permissions"></a>사용 권한

이 기능을 등록하거나 제거하려면 Azure 사용자가 구독의 소유자 또는 기여자 역할의 구성원이어야 합니다.

## <a name="examples"></a>예제

다음 섹션에서는 Azure Portal에서 Microsoft.Sql 리소스 공급자에 미리 보기 기능을 등록하거나 등록 취소하는 방법을 설명합니다. 

### <a name="register-block-t-sql-crud"></a>블록 T-SQL CRUD 등록

1. Azure Portal에서 구독으로 이동합니다.
2. **미리 보기 기능** 탭을 선택합니다. 
3. **블록 T-SQL CRUD** 를 선택합니다.
4. **블록 T-SQL CRUD** 를 선택하면 새 창이 열리고 **등록** 을 선택하여 이 블록을 Microsoft.Sql 리소스 공급자에 등록합니다.

![미리 보기 기능 목록에서 "블록 T-SQL CRUD"를 선택합니다.](./media/block-tsql-crud/block-tsql-crud.png)

!["블록 T-SQL CRUD"를 선택한 상태에서 등록을 선택합니다.](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Microsoft.sql 리소스 공급자 다시 등록 
T-SQL CRUD의 블록을 Microsoft.Sql 리소스 공급자에 등록한 후 변경 내용을 적용하려면 Microsoft.Sql 리소스 공급자를 다시 등록해야 합니다. Microsoft.Sql 리소스 공급자를 다시 등록하려면 다음을 수행합니다.

1. Azure Portal에서 구독으로 이동합니다.
2. **리소스 공급자** 탭을 선택합니다.
3. **Microsoft.Sql** 리소스 공급자를 검색하고 선택합니다.
4. **다시 등록** 을 선택합니다. 

> [!NOTE]
> T-SQL 블록을 구독에 적용하려면 다시 등록 단계가 필수입니다. 

![Microsoft.Sql 리소스 공급자 다시 등록](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>블록 T-SQL CRUD 제거
구독에서 T-SQL 생성 또는 수정 작업의 블록을 제거하려면 먼저 이전에 등록된 T-SQL 블록의 등록을 취소합니다. 그런 다음, T-SQL 블록을 제거하기 위해 위와 같이 Microsoft.Sql 리소스 공급자를 다시 등록합니다. 


## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 보안 기능 개요](security-overview.md)
- [Azure SQL Database 보안 모범 사례](security-best-practice.md)