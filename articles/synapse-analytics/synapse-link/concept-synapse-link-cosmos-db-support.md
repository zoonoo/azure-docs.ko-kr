---
title: Azure Cosmos DB용 Azure Synapse Link(미리 보기) 지원 기능
description: Azure Cosmos DB용 Azure Synapse Link에서 지원하는 현재 작업 목록 이해
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: c9f6a61dfa688980fa6400a2fa93ab8862798750
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194776"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB용 Azure Synapse Link(미리 보기) 지원 기능

이 문서에서는 Azure Cosmos DB용 Azure Synapse Link에서 현재 지원되는 기능을 설명합니다.

## <a name="azure-synapse-support"></a>Azure Synapse 지원

Azure Cosmos DB에는 다음과 같은 두 가지 유형의 컨테이너가 있습니다.
* HTAP 컨테이너 - Synapse Link가 활성화된 컨테이너입니다. 이 컨테이너에는 트랜잭션 저장소와 분석 저장소가 모두 있습니다. 
* OLTP 컨테이너 - 트랜잭션 저장소만 있는 컨테이너, Synapse Link가 활성화되지 않았습니다. 

> [!IMPORTANT]
> Azure Cosmos DB용 Azure Synapse Link는 현재 관리형 가상 네트워크가 활성화되지 않은 작업 영역에서 지원됩니다. 

Synapse Link를 활성화하지 않고 Azure Cosmos DB 컨테이너에 연결할 수 있습니다. 이 경우 트랜잭션 저장소에 대한 읽기/쓰기만 가능합니다. Azure Cosmos DB용 Synapse Link 내에서 현재 지원되는 기능 목록은 다음과 같습니다. 

| 범주              | 설명 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL 서버리스](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **런타임 지원** |Azure Synapse 런타임에 의한 읽기 또는 쓰기 지원| ✓ | [문의처](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Azure Cosmos DB API 지원** |Synapse Link로 API 지원| SQL / MongoDB | SQL / MongoDB |
| **Object**  |Azure Cosmos DB 컨테이너를 직접 가리키면서 만들 수 있는 테이블과 같은 개체| 보기, 테이블 | 보기 |
| **읽기**    |Azure Cosmos DB 컨테이너에서 데이터 읽기| OLTP / HTAP | HTAP  |
| **쓰기**   |런타임에서 Azure Cosmos DB 컨테이너에 데이터 쓰기| OLTP | 해당 없음 |

* Spark에서 Azure Cosmos DB 컨테이너에 데이터를 작성하는 경우 이 프로세스는 Azure Cosmos DB의 트랜잭션 저장소를 통해 수행되며 요청 단위를 사용하여 Azure Cosmos DB의 트랜잭션 성능에 영향을 줍니다.
* 외부 테이블을 통한 SQL 풀 통합은 현재 지원되지 않습니다.

## <a name="supported-code-generated-actions-for-spark"></a>Spark에 대해 지원되는 코드 생성 작업

| 제스처              | 설명 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **DataFrame에 로드** |Spark DataFrame에 데이터 로드 및 읽기 |X| ✓ |
| **Spark 테이블 만들기** |Azure Cosmos DB 컨테이너를 가리키는 테이블 만들기|X| ✓ |
| **컨테이너에 DataFrame 쓰기** |컨테이너에 데이터 쓰기|✓| ✓ |
| **컨테이너에서 스트리밍 DataFrame 로드** |Azure Cosmos DB 변경 피드를 사용하여 데이터 스트리밍|✓| ✓ |
| **컨테이너에 스트리밍 DataFrame 쓰기** |Azure Cosmos DB 변경 피드를 사용하여 데이터 스트리밍|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL 서버리스에 대해 지원되는 코드 생성 작업

| 제스처              | 설명 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **상위 100개 선택** |컨테이너에서 상위 100개 항목 미리 보기|X| ✓ |
| **뷰 만들기** |Synapse SQL을 통해 컨테이너에서 직접 BI에 액세스할 수 있는 뷰 만들기|X| ✓ |

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB용 Synapse Link에 연결](../quickstart-connect-synapse-link-cosmos-db.md)하는 방법을 참조하세요.
* [Spark를 사용하여 분석 저장소를 쿼리하는 방법 알아보기](how-to-query-analytical-store-spark.md)