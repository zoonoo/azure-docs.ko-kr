---
title: '문제 해결: 서버 리스 SQL 풀을 사용 하 여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트 읽기'
description: Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀을 사용 하 여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트 읽기
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466935"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀을 사용 하 여 CSV 또는 Parquet 파일에서 UTF-8 텍스트 읽기 문제 해결

이 문서에서는 Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀을 사용 하 여 CSV 또는 Parquet 파일에서 UTF-8 텍스트를 읽기 위한 문제 해결 단계를 제공 합니다.

서버를 사용 하지 않는 SQL 풀을 사용 하 여 CSV 또는 PARQUET 파일에서 UTF-8 텍스트를 읽으면 쿼리에서 UTF8이 아닌 데이터 정렬을 사용 하 여 VARCHAR 열을 반환 하는 경우에는 ü 및 ö와 같은 일부 특수 문자가 올바르게 변환 되지 않습니다. 이는 SQL Server 및 Azure SQL의 알려진 문제입니다. UTF8이 아닌 데이터 정렬은 Synapse SQL의 기본값 이므로 고객 쿼리에 영향을 줍니다. 표준 영어 문자를 사용 하 고 확장 된 라틴 문자의 일부 하위 집합을 사용 하는 고객은 변환 오류를 확인할 수 없습니다. 잘못 된 변환에 대해서는 [항상 utf-8 데이터 정렬을 사용 하 여 서버를 사용 하지 않는 SQL 풀에서 utf-8 텍스트를 읽도록](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633) 자세히 설명 합니다.

## <a name="workaround"></a>해결 방법

이 문제에 대 한 해결 방법은 CSV 또는 PARQUET 파일에서 UTF-8 텍스트를 읽을 때 항상 UTF-8 데이터 정렬을 사용 하는 것입니다.

-   대부분의 경우 데이터베이스에서 UTF8 데이터 정렬을 설정 해야 합니다 (메타 데이터 작업).
-   UTF8 데이터를 읽는 외부 테이블에 UTF8 데이터 정렬을 지정 하지 않은 경우에는 영향을 받는 외부 테이블을 다시 만들고 VARCHAR 열에 UTF8 데이터 정렬을 설정 해야 합니다 (메타 데이터 작업).


## <a name="next-steps"></a>다음 단계

* [Synapse SQL의 CETAS](../sql/develop-tables-cetas.md)
* [빠른 시작: 서버리스 SQL 풀 사용](../quickstart-sql-on-demand.md)
