---
title: 개요 - SQL 주문형(미리 보기)을 사용하여 스토리지의 데이터 쿼리
description: 이 섹션에는 Azure Synapse Analytics 내에서 SQL 주문형(미리 보기) 리소스를 사용해 볼 수 있는 샘플 쿼리가 포함되어 있습니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d42dac0899c5ab9c4f817df40095dd08891fb8e7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207705"
---
# <a name="overview-query-data-in-storage"></a>개요: 스토리지의 데이터 쿼리

이 섹션에는 Azure Synapse Analytics 내에서 SQL 주문형(미리 보기) 리소스를 사용해 볼 수 있는 샘플 쿼리가 포함되어 있습니다.
현재 지원되는 파일 형식은 다음과 같습니다.  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>필수 구성 요소

쿼리를 실행하는 데 필요한 도구:

- 원하는 SQL 클라이언트:
    - Azure Synapse Studio(미리 보기)
    - Azure Data Studio
    - SQL Server Management Studio

그리고 매개 변수는 다음과 같습니다.

| 매개 변수                                 | 설명                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 주문형 서비스 엔드포인트 주소    | 서버 이름으로 사용됩니다.                                   |
| SQL 주문형 서비스 엔드포인트 영역     | 샘플에 사용할 스토리지를 확인하는 데 사용됩니다. |
| 엔드포인트 액세스를 위한 사용자 이름 및 암호 | 엔드포인트 액세스하는 데 사용됩니다.                               |
| 보기를 만드는 데 사용할 데이터베이스     | 이 데이터베이스는 샘플의 시작점으로 사용됩니다.       |

## <a name="first-time-setup"></a>처음 설정

첫 번째 단계는 쿼리를 실행할 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플의 데이터를 읽는 데 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

> [!NOTE]
> 데이터베이스는 실제 데이터가 아닌 메타데이터를 보는 용도로만 사용됩니다.  사용할 데이터베이스 이름을 적어 둡니다. 나중에 필요합니다.

```sql
CREATE DATABASE mydbname;
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

### <a name="sample-query"></a>샘플 쿼리

유효성 검사의 마지막 단계는 다음 쿼리를 실행하는 것입니다.

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
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
