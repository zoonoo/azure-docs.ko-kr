---
title: Power BI Professional에 연결
description: 이 자습서에서는 Power BI 데스크톱을 SQL 주문형(미리 보기)에 연결하는 방법의 단계를 안내합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 497c6f669699f1e1ce879c0c7da2593256b97448
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501102"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Power BI Professional을 사용하여 Synapse SQL에 연결

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

이 자습서에서는 Power BI 데스크톱을 SQL 주문형(미리 보기)에 연결하는 단계를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

쿼리를 실행하는 도구:

- 원하는 SQL 클라이언트:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI 데스크톱이 설치되어 있어야 합니다.

매개 변수

| 매개 변수                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 주문형 서비스 엔드포인트 주소    | 서버 이름으로 사용됩니다.                                   |
| SQL 주문형 서비스 엔드포인트 영역     | 샘플에 사용할 스토리지를 결정하는 데 사용됩니다. |
| 엔드포인트 액세스를 위한 사용자 이름 및 암호 | 엔드포인트 액세스하는 데 사용됩니다.                               |
| 보기를 만드는 데 사용할 데이터베이스     | 이 데이터베이스는 샘플의 시작점으로 사용됩니다.       |

## <a name="first-time-setup"></a>처음 설정

샘플을 사용하기 전에 두 단계가 있습니다.

1. 보기에 대한 데이터베이스 만들기
2. SQL 주문형에서 스토리지의 파일에 액세스하는 데 사용할 자격 증명 만들기

### <a name="create-database"></a>데이터베이스 만들기

이 시작 문서에서는 데모로 사용할 사용자 고유의 데이터베이스를 만들어야 합니다. 보기 생성에는 데이터베이스가 필요합니다. 이 설명서 내의 일부 샘플 쿼리에서 이 데이터베이스를 사용합니다.

> [!NOTE]
> 데이터베이스는 실제 데이터가 아닌 메타데이터를 보는 용도로만 사용됩니다.
>
> 나중에 필요하므로 사용 중인 데이터베이스 이름을 적어 둡니다.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>자격 증명 만들기

쿼리를 실행하려면 먼저 자격 증명을 만들어야 합니다. 이 자격 증명은 SQL 주문형 서비스에서 스토리지의 파일에 액세스하는 데 사용됩니다.

> [!NOTE]
> 스토리지 계정에 액세스하기 위한 자격 증명을 만들어야 합니다. SQL 주문형은 다른 영역의 스토리지에 액세스할 수 있지만, 스토리지와 Azure Synapse 작업 영역이 같은 영역에 있으면 보다 나은 성능 환경이 제공됩니다.

**인구 조사 데이터 컨테이너에 대한 자격 증명을 만드는 방법에 대한 코드 조각** 다음을 실행합니다.

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Power BI 데스크톱 보고서 만들기

Power BI 데스크톱 애플리케이션을 열고 **데이터 가져오기** 옵션을 선택합니다.

![Power BI Desktop 애플리케이션을 열고 [데이터 가져오기] 선택](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>1단계 - 데이터 원본 선택

메뉴에서 **Azure**를 선택한 다음, **Azure SQL Database**를 선택합니다.
![데이터 원본을 선택합니다.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>2단계 - 데이터베이스 선택

데이터베이스의 URL과 보기가 있는 데이터베이스의 이름을 작성합니다.
![엔드포인트에서 데이터베이스를 선택합니다.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>다음 단계

Azure Data Studio를 사용하여 SQL 주문형에 연결하는 방법을 알아보려면 [스토리지 파일 쿼리](get-started-azure-data-studio.md)로 이동합니다.
 