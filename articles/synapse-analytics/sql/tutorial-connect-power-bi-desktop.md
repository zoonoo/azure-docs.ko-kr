---
title: '자습서: Power BI Desktop에 SQL 주문형(미리 보기)을 연결하고 보고서 만들기'
description: 이 자습서에서는 Azure Synapse Analytics의 SQL 주문형(미리 보기)를 Power BI Desktop에 연결하고, 보기 기반의 데모 보고서를 만드는 방법을 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769489"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>자습서: Power BI Desktop에 SQL 주문형(미리 보기)을 연결하고 보고서 만들기

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - 데모 데이터베이스 만들기
> - 보고서에 사용되는 보기 만들기
> - Power BI Desktop에 연결
> - 보기 기반의 보고서 만들기

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 소프트웨어가 필요합니다.

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 또는 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) 같은 SQL 쿼리 도구
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

다음 매개 변수의 값:

| 매개 변수                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 주문형 서비스 엔드포인트 주소    | 서버 이름으로 사용됩니다.                                   |
| SQL 주문형 서비스 엔드포인트 영역     | 샘플에 사용할 스토리지를 확인하는 데 사용 |
| 엔드포인트 액세스를 위한 사용자 이름 및 암호 | 엔드포인트 엑세스에 사용                               |
| 보기를 만드는 데 사용할 데이터베이스     | 샘플에서 시작점으로 사용되는 데이터베이스       |

## <a name="1---create-database"></a>1 - 데이터베이스 만들기

데모 환경에서는 사용자 고유의 데모 데이터베이스를 만듭니다. 이 데이터베이스는 실제 데이터를 저장하는 목적이 아닌 메타데이터를 보는 용도로 사용됩니다.

다음 T-SQL(Transact-SQL) 스크립트를 실행하여 데모 데이터베이스를 만듭니다(그리고 필요한 경우 기존 데이터베이스를 삭제).

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - 자격 증명 만들기

SQL 주문형 서비스에서 스토리지의 파일에 액세스하려면 자격 증명이 필요합니다. 엔드포인트와 동일한 영역에 있는 스토리지 계정의 자격 증명을 만듭니다. SQL 주문형은 다른 영역의 스토리지 계정에 액세스할 수 있지만, 스토리지와 엔드포인트가 같은 영역에 있으면 성능이 향상됩니다.

다음 T-SQL(Transact-SQL) 스크립트를 실행하여 자격 증명을 만듭니다.

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - 보기 준비

다음 T-SQL(Transact-SQL) 스크립트를 실행하여 Power BI에서 사용할 외부 데모 데이터를 기반으로 보기를 만듭니다.

다음 쿼리를 사용하여 `Demo` 데이터베이스 내부에 `usPopulationView` 보기를 만듭니다.

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

데모 데이터에는 다음 데이터 세트가 포함되어 있습니다.

2000년에서 2010년까지 십년간의 인구 조사에서 parquet 형식으로 출처를 얻은 미국의 각 카운티에 대한 성별 및 인종별 인구

| 폴더 경로                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | 데모 스토리지 계정의 데이터에 대한 부모 폴더               |
| /release/us_population_county/                               | Hive/Hadoop 파티션 구성표를 사용하여 연도별로 분할된 Parquet 형식의 미국 인구 데이터 파일입니다. |

## <a name="4---create-power-bi-report"></a>4 - Power BI 보고서 만들기

다음 단계에 따라 Power BI Desktop에 대한 보고서를 만듭니다.

1. Power BI Desktop 애플리케이션을 열고 **데이터 가져오기**를 선택합니다.

   ![Power BI Desktop 애플리케이션을 열고 [데이터 가져오기] 선택](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. **Azure** > **Azure SQL Database**를 선택합니다. 

   ![데이터 원본을 선택합니다.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. 데이터베이스가 있는 서버 이름을 **서버** 필드에 입력한 다음, 데이터베이스 이름에 `Demo`를 입력합니다. **가져오기** 옵션을 선택한 다음, **확인**을 선택합니다. 

   ![엔드포인트에서 데이터베이스를 선택합니다.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. 원하는 인증 방법을 선택합니다.

    - AAD 예제 
  
    ![[로그인]을 클릭합니다.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - SQL 로그인 예제 - 사용자 이름 및 암호를 입력합니다.

    ![SQL 로그인 사용](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. `usPopulationView` 보기를 선택한 다음, **로드**를 선택합니다. 

   ![선택한 데이터베이스에서 보기를 선택합니다.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. 작업이 완료될 때까지 기다리면 `There are pending changes in your queries that haven't been applied`라는 팝업이 표시됩니다. **변경 내용 적용**을 선택합니다. 

   ![[변경 내용 적용] 클릭](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. **쿼리 변경 내용 적용** 대화 상자가 사라질 때까지 기다립니다. 몇 분 정도 걸릴 수 있습니다. 

   ![쿼리가 완료될 때까지 대기](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. 로드가 완료되면 다음 열을 순서대로 선택하여 보고서를 만듭니다.
   - countyName
   - 채우기(population)
   - stateName

   ![원하는 열을 선택하여 맵 보고서를 생성합니다.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>리소스 정리

이 보고서 사용을 마쳤으면 다음 단계에 따라 리소스를 삭제합니다.

1. 스토리지 계정의 자격 증명 삭제

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. 보기 삭제

   ```sql
   DROP VIEW usPopulationView;
   ```

3. 데이터베이스 삭제

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>다음 단계

[스토리지 파일 쿼리](develop-storage-files-overview.md)로 넘어가서 Synapse SQL을 사용하여 스토리지 파일을 쿼리하는 방법을 알아보세요.
