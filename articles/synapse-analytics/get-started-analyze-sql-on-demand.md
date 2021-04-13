---
title: '자습서: 서버리스 SQL 풀을 사용하여 데이터 분석 시작'
description: 이 자습서에서는 Spark 데이터베이스에 있는 데이터를 사용하여 서버리스 SQL 풀로 데이터를 분석하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 0ef76be2c083d6185b8b919d174de28aa3b65446
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293773"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 데이터 분석

이 자습서에서는 서버리스 SQL 풀로 데이터를 분석하는 방법을 알아봅니다. 

## <a name="the-built-in-serverless-sql-pool"></a>기본 제공 서버리스 SQL 풀

서버리스 SQL 풀을 사용하면 용량을 예약하지 않고도 SQL을 사용할 수 있습니다. 서버리스 SQL 풀에 대한 요금 청구는 쿼리를 실행하는 데 사용된 노드 수가 아니라 쿼리를 실행하는 데 처리된 데이터 양을 기준으로 합니다.

모든 작업 영역에는 **기본 제공** 이라는 미리 구성된 서버리스 SQL 풀이 제공됩니다. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>서버리스 SQL 풀을 사용하여 Blob 스토리지에서 NYC Taxi 데이터 분석

이 섹션에서는 서버리스 SQL 풀을 사용하여 Azure Blob Storage 계정의 NYC Taxi 데이터를 분석합니다.

1. Synapse Studio에서 **개발** 허브로 이동합니다.
1. 새 SQL 스크립트를 만듭니다.
1. 다음 코드를 스크립트에 붙여 넣습니다.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. **실행** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [서버리스 Spark 풀을 사용하여 데이터 분석](get-started-analyze-spark.md)
