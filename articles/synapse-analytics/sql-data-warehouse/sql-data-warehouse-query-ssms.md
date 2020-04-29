---
title: SSMS를 사용하여 연결
description: SSMS (SQL Server Management Studio)를 사용 하 여 Azure Synapse Analytics에 연결 하 고 쿼리 합니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743855"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>SSMS (SQL Server Management Studio)를 사용 하 여 Azure Synapse Analytics에 연결

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

SSMS (SQL Server Management Studio)를 사용 하 여 Azure Synapse 내에서 데이터 웨어하우스에 연결 하 고 쿼리 합니다.

## <a name="prerequisites"></a>전제 조건

이 자습서를 사용하려면 다음이 필요합니다.

* 기존 SQL 풀. 만들려면 [SQL 풀 만들기](create-data-warehouse-portal.md)를 참조하세요.
* SSMS(SQL Server Management Studio) 설치됨. 아직 없는 경우 SSMS를 무료로 [다운로드](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 하세요.
* 정규화된 SQL 서버 이름. 이 정보를 찾으려면 [SQL 풀에 연결](sql-data-warehouse-connect-overview.md)을 참조 하세요.

## <a name="1-connect-to-your-sql-pool"></a>1. SQL 풀에 연결

1. SSMS를 엽니다.
2. **파일** > **연결 개체 탐색기**을 선택 하 여 개체 탐색기을 엽니다.

    ![SQL Server 개체 탐색기](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. 서버 창에 연결에서 필드를 입력합니다.

   ![서버에 연결](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **서버 이름**입니다. 이전에 식별한 **서버 이름** 을 입력합니다.
   * **인증**: **SQL Server 인증** 또는 **Active Directory 통합 인증**을 선택합니다.
   * **사용자 이름** 및 **암호**입니다. 위에서 SQL Server 인증을 선택한 경우 사용자 이름 및 암호를 입력합니다.
   * **연결**을 클릭합니다.
4. 탐색하려면 SQL Azure Server를 확장합니다. 서버와 연결된 데이터베이스를 볼 수 있습니다. AdventureWorksDW를 확장하여 샘플 데이터베이스의 테이블을 확인합니다.

   ![AdventureWorksDW 탐색하기](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. 샘플 쿼리 실행

데이터베이스에 대한 연결을 설정했으므로 쿼리를 작성합니다.

1. SQL Server 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다.
2. **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.

   ![새 쿼리](./media/sql-data-warehouse-query-ssms/new-query.png)
3. 다음 T-SQL 쿼리를 쿼리 창에 복사합니다.

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. 을 클릭 `Execute` 하 여 쿼리를 실행 하거나 다음 바로 가기를 `F5`사용 합니다.

   ![쿼리 실행](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. 쿼리 결과를 봅니다. 이 예에서 FactInternetSales 테이블에는 60398 행이 있습니다.

   ![쿼리 결과](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>다음 단계

이제 연결 및 쿼리할 수 있으므로 [Power BI로 데이터 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md)를 시도해 보세요. Azure Active Directory 인증을 위한 환경을 구성 하려면 [SQL 풀에 인증](sql-data-warehouse-authentication.md)을 참조 하세요.
