---
title: '자습서: SQL 주문형을 사용하여 데이터 분석 시작하기'
description: 이 자습서에서는 Spark 데이터베이스에 있는 데이터를 사용하여 SQL 주문형 데이터를 분석하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093821"
---
# <a name="analyze-data-with-sql-on-demand"></a>SQL 주문형 데이터 분석

이 자습서에서는 Spark 데이터베이스에 있는 데이터를 사용하여 SQL 주문형 데이터를 분석하는 방법을 알아봅니다. 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL 주문형을 사용하여 Spark 데이터베이스에서 NYC 택시 데이터 분석

Spark 데이터베이스의 테이블은 자동으로 표시되며, SQL 주문형에서 이를 쿼리할 수 있습니다.

1. Synapse Studio에서 **개발** 허브로 이동하여 새 SQL 스크립트를 만듭니다.
1. **연결 대상**을 **SQL 주문형**으로 설정합니다.
1. 다음 텍스트를 스크립트에 붙여넣고, 해당 스크립트를 실행합니다.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > SQL 주문형을 사용하는 쿼리를 처음 실행하는 경우 SQL 주문형에서 쿼리를 실행하는 데 필요한 SQL 리소스를 수집하는 데 약 10초 정도 걸립니다. 이후 쿼리는 훨씬 더 빠릅니다.
  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Spark를 사용하여 분석](get-started-analyze-spark.md)
