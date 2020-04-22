---
title: 개요 - SQL 주문형(미리 보기)을 사용하여 스토리지의 데이터 쿼리
description: 이 섹션에는 Azure Synapse Analytics 내에서 SQL 주문형(미리 보기) 리소스를 사용해 볼 수 있는 샘플 쿼리가 포함되어 있습니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421297"
---
# <a name="overview-query-data-in-storage"></a>개요: 스토리지의 데이터 쿼리

이 섹션에는 Azure Synapse Analytics 내에서 SQL 주문형(미리 보기) 리소스를 사용해 볼 수 있는 샘플 쿼리가 포함되어 있습니다.
현재 지원되는 파일은 다음과 같습니다. 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>사전 요구 사항

쿼리를 실행하는 데 필요한 도구:

- 원하는 SQL 클라이언트:
    - Azure Synapse Studio(미리 보기)
    - Azure Data Studio
    - SQL Server Management Studio

그리고 매개 변수는 다음과 같습니다.

| 매개 변수                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 주문형 서비스 엔드포인트 주소    | 서버 이름으로 사용됩니다.                                   |
| SQL 주문형 서비스 엔드포인트 영역     | 샘플에 사용할 스토리지를 확인하는 데 사용됩니다. |
| 엔드포인트 액세스를 위한 사용자 이름 및 암호 | 엔드포인트 액세스하는 데 사용됩니다.                               |
| 보기를 만드는 데 사용할 데이터베이스     | 이 데이터베이스는 샘플의 시작점으로 사용됩니다.       |

## <a name="first-time-setup"></a>처음 설치

이 문서의 뒷부분에 포함된 샘플을 사용하기 전에 다음 두 단계를 수행합니다.

- 보기에 대한 데이터베이스 만들기(보기를 사용하려는 경우)
- SQL 주문형에서 스토리지의 파일에 액세스하는 데 사용할 자격 증명 만들기

### <a name="create-database"></a>데이터베이스 만들기

보기를 만들려면 데이터베이스가 필요합니다. 이 데이터베이스는 이 설명서의 샘플 쿼리에 사용됩니다.

> [!NOTE]
> 데이터베이스는 실제 데이터가 아닌 메타데이터를 보는 용도로만 사용됩니다.  사용할 데이터베이스 이름을 적어 둡니다. 나중에 필요합니다.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>자격 증명 만들기

쿼리를 실행하려면 먼저 자격 증명을 만들어야 합니다. 이 자격 증명은 SQL 주문형 서비스에서 스토리지의 파일에 액세스하는 데 사용됩니다.

> [!NOTE]
> 이 섹션의 방법을 성공적으로 실행하려면 SAS 토큰을 사용해야 합니다.
>
> SAS 토큰을 사용하려면 UserIdentity를 삭제해야 하며, 이에 대한 내용은 다음 [문서](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)에 설명되어 있습니다.
>
> 기본적으로 SQL 주문형은 항상 AAD 통과를 사용합니다.

스토리지 액세스 제어를 관리하는 방법에 대한 자세한 내용은 이 [링크](develop-storage-files-storage-access-control.md)를 확인하세요.

> [!WARNING]
> 엔드포인트 영역에 있는 스토리지 계정의 자격 증명을 만들어야 합니다. SQL 주문형은 다른 영역의 스토리지에 액세스할 수 있지만, 스토리지와 엔드포인트가 같은 영역에 있으면 보다 나은 성능 환경이 제공됩니다.

CSV, JSON 및 Parquet 컨테이너에 대한 자격 증명을 만들려면 아래 코드를 실행합니다.

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>제공된 데모 데이터

데모 데이터에는 다음 데이터 세트가 포함되어 있습니다.

- 뉴욕시 택시 - 노란색 택시 주행 기록 - 뉴욕시 공용 데이터 세트의 일부
  - CSV 형식
  - Parquet 형식
- 인구 데이터 세트
  - CSV 형식
- 중첩 열이 있는 샘플 Parquet 파일
  - Parquet 형식
- 서적 JSON
  - JSON 형식

| 폴더 경로                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | CSV 형식의 데이터에 대한 부모 폴더                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 다른 CSV 형식의 인구 데이터 파일이 있는 폴더 |
| /csv/taxi/                                                   | CSV 형식의 뉴욕시 공용 데이터 파일이 있는 폴더              |
| /parquet/                                                    | Parquet 형식의 데이터에 대한 부모 폴더                     |
| /parquet/taxi                                                | Hive/Hadoop 파티션 구성표를 사용하여 연도 및 월별로 분할된 Parquet 형식의 뉴욕시 공용 데이터 파일입니다. |
| /parquet/nested/                                             | 중첩 열이 있는 샘플 Parquet 파일                     |
| /json/                                                       | JSON 형식의 데이터에 대한 부모 폴더                        |
| /json/books/                                                 | 서적 데이터가 포함된 JSON 파일                                   |

## <a name="validation"></a>유효성 검사

다음 세 개 쿼리를 실행하여 자격 증명이 올바르게 생성되었는지 확인합니다.

> [!NOTE]
> 샘플 쿼리의 모든 URI는 북유럽 Azure 지역에 있는 스토리지 계정을 사용합니다. 적절한 자격 증명을 만들었는지 확인합니다. 아래 쿼리를 실행하고 스토리지 계정이 나열되는지 확인합니다.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

적절한 자격 증명을 찾을 수 없으면 [처음 설치](#first-time-setup)를 확인합니다.

### <a name="sample-query"></a>샘플 쿼리

유효성 검사의 마지막 단계는 다음 쿼리를 실행하는 것입니다.

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

위의 쿼리는 숫자 **8945574**를 반환해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 방법 문서를 진행할 수 있습니다.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)

- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)

- [특정 파일 쿼리](query-specific-files.md)

- [Parquet 파일 쿼리](query-parquet-files.md)

- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)

- [JSON 파일 쿼리](query-json-files.md)

- [보기 만들기 및 사용](create-use-views.md)
