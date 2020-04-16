---
title: Visual Studio 및 SSDT를 사용하여 Synapse SQL에 연결하고 쿼리합니다.
description: Visual Studio를 사용하여 Azure 시냅스 분석을 사용하여 SQL 풀을 쿼리합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428564"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>비주얼 스튜디오 및 SSDT로 시냅스 SQL에 연결
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Visual Studio를 사용하여 Azure 시냅스 분석을 사용하여 SQL 풀을 쿼리합니다. 이 메서드는 Visual Studio 2019에서 SQL 서버 데이터 도구(SSDT) 확장을 사용합니다. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>SQL 온디맨드 지원 도구(미리 보기)

현재 SQL 온디맨드(미리 보기)에는 Visual Studio가 지원되지 않습니다. 그러나 Azure 데이터 스튜디(미리 보기)o는 완전히 지원되는 도구입니다. SQL Server 관리 Studio는 버전 18.4에서 부분적으로 지원되며 연결 및 쿼리와 같은 기능이 제한되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 사용 하려면 다음 구성 요소가 있어야 합니다.

* 기존 SQL 풀입니다. 없는 경우 이 필수 구성 조건을 완료하기 위해 [SQL 풀 만들기를](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 참조하십시오.
* Visual Studio용 SSDT. Visual Studio가 있는 경우 이미 이 구성 요소가 있을 수 있습니다. 설치 지침 및 옵션은 [Visual Studio 및 SSDT 설치](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.
* 정규화된 SQL 서버 이름. 이를 찾으려면 [SQL 풀에 연결 을](connect-overview.md)참조하십시오.

## <a name="1-connect-to-sql-pool"></a>1. SQL 풀에 연결
1. Visual Studio 2019를 엽니다.
2. SQL Server 개체 탐색기를 엽니다. 이렇게 하려면**SQL Server 개체 탐색기** **보기를** > 선택합니다.
   
    ![SQL Server 개체 탐색기](./media/get-started-visual-studio/open-ssdt.png)
3. **SQL Server 추가** 아이콘을 클릭합니다.
   
    ![SQL Server 추가](./media/get-started-visual-studio/add-server.png)
4. 서버 창에 연결에서 필드를 입력합니다.
   
    ![서버에 연결](./media/get-started-visual-studio/connection-dialog.png)
   
   * **서버 이름**: 이전에 식별된 **서버 이름을** 입력합니다.
   * **인증**: **SQL 서버 인증** 또는 활성 **디렉터리 통합 인증**선택 :
   * **사용자 이름** 및 **암호**: 위에서 SQL Server 인증을 선택한 경우 사용자 이름과 암호를 입력합니다.
   * **연결**을 클릭합니다.
5. 탐색하려면 SQL Azure Server를 확장합니다. 서버와 연결된 데이터베이스를 볼 수 있습니다. AdventureWorksDW를 확장하여 샘플 데이터베이스의 테이블을 확인합니다.
   
    ![AdventureWorksDW 탐색하기](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 샘플 쿼리 실행
데이터베이스에 대한 연결이 설정되었으므로 쿼리를 작성합니다.

1. SQL Server 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다.
2. **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.
   
    ![새 쿼리](./media/get-started-visual-studio/new-query2.png)
3. 다음 T-SQL 쿼리를 쿼리 창에 복사합니다.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 쿼리를 실행합니다. 이렇게 하려면 녹색 화살표를 클릭하거나 다음 바로 가기를 사용합니다. `CTRL`+`SHIFT`+`E`
   
    ![쿼리 실행](./media/get-started-visual-studio/run-query.png)
5. 쿼리 결과를 봅니다. 이 예에서 FactInternetSales 테이블에는 60398 행이 있습니다.
   
    ![쿼리 결과](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>다음 단계
이제 연결하고 쿼리할 수 있으므로 [Power BI를 사용하여 데이터를 시각화해](get-started-power-bi-professional.md)보십시오.
Azure Active Directory 인증에 대한 환경을 구성하려면 [SQL 풀에 대한 인증 을](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)참조하십시오.
 