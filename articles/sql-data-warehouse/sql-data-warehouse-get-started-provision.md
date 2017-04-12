---
title: "Azure 포털에서 SQL Data Warehouse 만들기 | Microsoft Docs"
description: "Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 75f4792ff2aa2b73ebc3ff976887a74ce09988f3
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL 데이터 웨어하우스 만들기
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

이 자습서에서는 Azure 포털을 사용하여 AdventureWorksDW 샘플 데이터베이스를 포함하는 SQL 데이터 웨어하우스를 만듭니다.

## <a name="prerequisites"></a>필수 조건
시작하려면 다음이 필요합니다.

* **Azure 계정**: [Azure 무료 평가판][Azure Free Trial] 또는 [MSDN Azure 크레딧][MSDN Azure Credits]을 방문하여 계정을 만듭니다.
* **Azure SQL Server**: 자세한 내용은 [Azure Portal을 사용하여 Azure SQL Database 만들기][Create an Azure SQL database in the Azure portal]를 참조하세요.

> [!NOTE]
> SQL Data Warehouse를 만들면 새로운 유료 서비스가 발생할 수 있습니다.  자세한 내용은 [SQL Data Warehouse 가격 책정][SQL Data Warehouse pricing]을 참조하세요.
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL 데이터 웨어하우스 만들기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **+ 새로 만들기** > **데이터베이스** > **SQL Data Warehouse**를 차례로 클릭합니다.

    ![생성](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. **SQL 데이터 웨어하우스** 블레이드에 필요한 정보를 입력한 다음 '만들기'를 눌러서 만듭니다.

    ![데이터베이스 만들기](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **서버**: 먼저 서버를 선택하는 것이 좋습니다.  
   * **데이터베이스 이름**: SQL 데이터 웨어하우스를 참조하는 데 사용되는 이름입니다.  서버에 대해 고유해야 합니다.
   * **성능**: 400[DWUs][DWU]로 시작하는 것이 좋습니다. 슬라이더를 왼쪽이나 오른쪽으로 이동하여 데이터 웨어하우스의 성능을 조정하거나, 만든 후에 확장 또는 축소할 수 있습니다.  DWU에 대한 자세한 내용은 [확장](sql-data-warehouse-manage-compute-overview.md) 또는 [가격 책정 페이지][SQL Data Warehouse pricing]에서 설명서를 참조하세요.
   * **구독**: SQL 데이터 웨어하우스가 청구될 [구독] 을 선택합니다.
   * **리소스 그룹**: [리소스 그룹][Resource group]은 컨테이너로, Azure 리소스의 컬렉션을 관리할 수 있도록 디자인되었습니다. [리소스 그룹](../azure-resource-manager/resource-group-overview.md)에 대해 알아봅니다.
   * **원본 선택**: **원본 선택** > **샘플**을 차례로 클릭합니다. Azure에서는 AdventureWorksDW를 사용하여 **선택 샘플** 옵션을 자동으로 채웁니다.

   > [!NOTE]
   > SQL 데이터 웨어하우스에 대한 기본 데이터 정렬은 SQL_Latin1_General_CP1_CI_AS입니다. 다른 데이터 정렬이 필요한 경우 다른 데이터 정렬이 포함된 데이터베이스를 만드는 데 [T-SQL][T-SQL]을 사용할 수 있습니다.
   >
   >

1. **만들기** 를 클릭하여 SQL 데이터 웨어하우스를 만듭니다.
2. 몇 분 정도 기다립니다. 데이터 웨어하우스가 준비되면 [Azure Portal](https://portal.azure.com)로 돌아가야 합니다. 생성 시 사용한 리소스 그룹 또는 SQL 데이터베이스 아래 나열된, 대시보드에서 SQL 데이터 웨어하우스를 찾을 수 있습니다.

    ![포털 보기](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스를 만들었으므로 [연결](sql-data-warehouse-connect-overview.md) 하여 쿼리할 준비가 되었습니다.

SQL 데이터 웨어하우스로 데이터를 로드하는 경우 [로드 개요](sql-data-warehouse-overview-load.md)를 참조하세요.

기존 데이터베이스를 SQL Data Warehouse로 마이그레이션하려는 경우 [마이그레이션 개요](sql-data-warehouse-overview-migrate.md)를 참조하거나 [마이그레이션 유틸리티](sql-data-warehouse-migrate-migration-utility.md)를 사용하세요.

Transact-SQL을 사용하여 방화벽 규칙을 구성할 수도 있습니다. 자세한 내용은 [sp_set_firewall_rule][sp_set_firewall_rule] 및 [sp_set_database_firewall_rule][sp_set_database_firewall_rule]을 참조하세요.

[모범 사례][Best practices]를 살펴보는 것도 좋습니다.

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[구독]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

