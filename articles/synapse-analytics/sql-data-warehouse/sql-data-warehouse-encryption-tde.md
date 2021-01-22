---
title: 전용 SQL 풀의 투명한 데이터 암호화 (포털) (이전의 SQL DW)
description: Azure Synapse Analytics의 전용 SQL 풀 (이전의 SQL DW)에 대 한 TDE (투명한 데이터 암호화)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: da4be6f4bc8335e0976a0a4a87c4d232b2a2285f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676309"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀 (이전의 SQL DW)에 대 한 TDE (투명한 데이터 암호화) 시작

> [!div class="op_single_selector"]
>
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>필요한 권한

TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## <a name="enabling-encryption"></a>암호화 설정

TDE를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 데이터베이스를 엽니다.
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명 한 데이터 암호화** 옵션 ![ 포털 설정을 선택 합니다.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 포털 설정 **설정에서 다음을 선택 합니다.** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5.  
    포털 설정 저장 선택 저장을 선택 합니다. ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>암호화 비활성화

TDE를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 데이터베이스를 엽니다.
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명 한 데이터 암호화** 옵션 ![ 포털 설정을 선택 합니다.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. **끄기** 설정 ![ 포털 설정 해제를 선택 합니다.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. **저장** 
    포털 설정 저장 2를 선택 합니다. ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>암호화 DMV

다음 DMV로 암호화를 확인할 수 있습니다.

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
