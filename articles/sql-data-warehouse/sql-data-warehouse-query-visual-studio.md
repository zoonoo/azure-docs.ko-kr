---
title: VSTS를 사용 하 여 연결
description: Visual Studio를 사용 하 여 Azure Synapse Analytics를 쿼리 합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 88dc534b8753311e49cafa9f84705258cdb0883d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198630"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Visual Studio 및 SSDT를 사용 하 여 Azure Synapse Analytics에 연결
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

몇 분만에 Visual Studio를 사용 하 여 Azure Synapse 내에서 SQL 풀을 쿼리 합니다. 이 메서드는 Visual Studio 2019에서 SSDT (SQL Server Data Tools) 확장을 사용 합니다. 

## <a name="prerequisites"></a>필수 조건
이 자습서를 사용하려면 다음이 필요합니다.

* 기존 SQL 풀. 하나를 만들려면 [SQL 풀 만들기](sql-data-warehouse-get-started-provision.md)를 참조 하세요.
* Visual Studio용 SSDT. Visual Studio가 있는 경우 Visual Studio 용 SSDT 이미 있는 것입니다. 설치 지침 및 옵션은 [Visual Studio 및 SSDT 설치](sql-data-warehouse-install-visual-studio.md)를 참조하세요.
* 정규화된 SQL 서버 이름. 이 정보를 찾으려면 [SQL 풀에 연결](sql-data-warehouse-connect-overview.md)을 참조 하세요.

## <a name="1-connect-to-your-sql-pool"></a>1. SQL 풀에 연결
1. Visual Studio 2019를 엽니다.
2.  > **SQL Server 개체 탐색기** **보기** 를 선택 하 여 SQL Server 개체 탐색기을 엽니다.
   
    ![SQL Server 개체 탐색기](media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. **SQL Server 추가** 아이콘을 클릭합니다.
   
    ![SQL Server 추가](media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 서버에 연결 창에서 필드를 입력합니다.
   
    ![서버에 연결](media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **서버 이름**. 이전에 식별한 **서버 이름** 을 입력합니다.
   * **인증**. **SQL Server 인증** 또는 **Active Directory 통합 인증**을 선택합니다.
   * **사용자 이름** 및 **암호**. 위에서 SQL Server 인증을 선택한 경우 사용자 이름 및 암호를 입력합니다.
   * **연결**을 클릭합니다.
5. 탐색하려면 Azure SQL Server를 확장합니다. 서버와 연결된 데이터베이스를 볼 수 있습니다. AdventureWorksDW를 확장하여 샘플 데이터베이스의 테이블을 확인합니다.
   
    ![AdventureWorksDW 탐색하기](media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 샘플 쿼리 실행
데이터베이스에 대한 연결을 설정했으므로 쿼리를 작성합니다.

1. SQL Server 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다.
2. **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.
   
    ![새 쿼리](media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 다음 T-sql 쿼리를 쿼리 창에 복사 합니다.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 녹색 화살표를 클릭 하 여 쿼리를 실행 하거나, `CTRL`+`SHIFT`+`E`를 사용 하 여 쿼리를 실행 합니다.
   
    ![쿼리 실행](media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 쿼리 결과를 봅니다. 이 예에서 FactInternetSales 테이블에는 60398 행이 있습니다.
   
    ![쿼리 결과](media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>다음 단계
이제 연결 하 고 쿼리할 수 있으므로 Power BI를 [사용 하 여 데이터를 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md)해 보세요.

Azure Active Directory 인증을 위한 환경을 구성 하려면 [SQL 풀에 인증](sql-data-warehouse-authentication.md)을 참조 하세요.