---
title: Azure 시냅스 SQL 아키텍처
description: Azure Synapse SQL이 MPP(대규모 병렬 처리)와 Azure 저장소를 결합하여 고성능 및 확장성을 달성하는 방법을 알아봅니다.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431814"
---
# <a name="azure-synapse-sql-architecture"></a>Azure 시냅스 SQL 아키텍처 

이 문서에서는 Synapse SQL의 아키텍처 구성 요소에 대해 설명합니다.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>시냅스 SQL 아키텍처 구성 요소

Synapse SQL은 확장 아키텍처를 활용하여 여러 노드에 데이터의 계산 처리를 배포합니다. 계산은 스토리지와 별개로 시스템의 데이터와 독립적으로 계산을 확장할 수 있습니다. 

SQL 풀의 경우 규모 단위는 [데이터 웨어하우스 단위로](resource-consumption-models.md)알려진 계산 능력의 추상화입니다. 

SQL 온디맨드의 경우 서버가 없는 경우 쿼리 리소스 요구 사항을 수용하기 위해 자동으로 크기 조정이 수행됩니다. 노드 또는 장애 조치(failovers)를 추가, 제거하여 시간이 지남에 따라 토폴로지가 변경됨에 따라 변경 사항에 적응하고 쿼리에 충분한 리소스가 있고 성공적으로 완료되도록 합니다. 예를 들어 아래 이미지는 4개의 계산 노드를 사용하여 쿼리를 실행하는 SQL 온디맨드 를 보여 주며 있습니다.

![시냅스 SQL 아키텍처](./media//overview-architecture/sql-architecture.png)

Synapse SQL은 노드 기반 아키텍처를 사용합니다. 응용 프로그램은 T-SQL 명령을 연결하고 Synapse SQL의 단일 입력 지점인 제어 노드에 발급합니다. 

SQL 풀 제어 노드는 MPP 엔진을 사용하여 병렬 처리를 위한 쿼리를 최적화한 다음 작업을 계산 노드에 전달하여 병렬로 작업을 수행합니다. 

SQL 온디맨드 제어 노드는 분산 쿼리 처리(DQP) 엔진을 사용하여 Compute 노드에서 실행되는 더 작은 쿼리로 분할하여 분산 된 사용자 쿼리 실행을 최적화하고 오케스트레이션합니다. 각 작은 쿼리는 작업이라고 하며 분산 실행 단위를 나타냅니다. 저장소에서 파일을 읽거나 다른 작업에서 결과를 조인하거나 다른 작업에서 검색한 데이터를 정렬합니다. 

컴퓨팅 노드는 모든 사용자 데이터를 Azure Storage에 저장하고 병렬 쿼리를 실행합니다. DMS(Data Movement Service)는 쿼리를 병렬로 실행하고 정확한 결과를 반환하기 위해 필요할 때 노드에서 데이터를 이동시키는 시스템 수준의 내부 서비스입니다. 

분리된 스토리지와 컴퓨팅을 사용하면 Synapse SQL을 사용할 때 스토리지 요구 사항에 관계없이 컴퓨팅 성능의 독립적인 크기 조정의 이점을 누릴 수 있습니다. SQL 주문형 크기 조정은 자동으로 수행되지만 SQL 풀의 경우 다음을 수행할 수 있습니다.

* 데이터를 이동하지 않고 SQL 풀(데이터 웨어하우스) 내에서 컴퓨팅 성능을 늘리거나 축소합니다.
* 데이터를 그대로 둔 채 컴퓨팅 용량을 일시 중지하여 스토리지 비용만 지불합니다.
* 운영 시간 동안 컴퓨팅 용량을 다시 시작합니다.

## <a name="azure-storage"></a>Azure Storage

시냅스 SQL은 Azure 저장소를 활용하여 사용자 데이터를 안전하게 유지합니다. Azure Storage에서 데이터를 저장하고 관리하므로 저장소 사용량에 대해 별도의 요금이 부과됩니다. 

SQL 온디맨드를 사용하면 데이터 레이크의 파일을 읽기 전용 방식으로 쿼리할 수 있으며 SQL 풀을 사용하면 데이터를 수집할 수도 있습니다. 데이터가 SQL 풀로 수집되면 데이터가 **분산되어** 시스템의 성능을 최적화합니다. 테이블을 정의할 때 데이터 분산에 사용할 분할 패턴을 선택할 수 있습니다. 이러한 샤딩 패턴은 지원됩니다.

* Hash
* 라운드 로빈
* 복제

## <a name="control-node"></a>제어 노드

컨트롤 노드는 아키텍처의 두뇌입니다. 모든 애플리케이션 및 연결과 상호 작용하는 프런트 엔드입니다. 

SQL 풀에서 MPP 엔진은 컨트롤 노드에서 실행되어 병렬 쿼리를 최적화하고 조정합니다. SQL 풀에 T-SQL 쿼리를 제출하면 Control 노드는 이를 각 배포에 대해 병렬로 실행되는 쿼리로 변환합니다.

주문형 SQL에서 DQP 엔진은 Control 노드에서 실행되어 Compute 노드에서 실행되는 더 작은 쿼리로 분할하여 분산 된 사용자 쿼리 실행을 최적화하고 조정합니다. 또한 각 노드에서 처리할 파일 집합을 할당합니다.

## <a name="compute-nodes"></a>컴퓨팅 노드

컴퓨팅 노드는 컴퓨팅 능력을 제공합니다. 

SQL 풀에서 분포는 처리를 위해 계산 노드에 매핑됩니다. 더 많은 계산 리소스에 대한 비용을 지불하면 풀은 사용 가능한 Compute 노드에 분포를 다시 매핑합니다. 계산 노드 수는 1에서 60사이이며 SQL 풀의 서비스 수준에 따라 결정됩니다. 각 컴퓨팅 노드에는 시스템 뷰에 표시되는 노드 ID가 있습니다. 시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 Compute 노드 ID를 볼 수 있습니다. 시스템 뷰 목록은 [MPP 시스템 뷰](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)를 참조하세요.

SQL 온디맨드에서 각 Compute 노드는 작업을 실행할 작업 및 파일 집합이 할당됩니다. 작업은 실제로 제출된 쿼리 사용자의 일부인 분산 쿼리 실행 단위입니다. 자동 크기 조정은 사용자 쿼리를 실행하는 데 충분한 Compute 노드를 사용할 수 있도록 적용됩니다.

## <a name="data-movement-service"></a>데이터 이동 서비스

데이터 이동 서비스(DMS)는 계산 노드 간의 데이터 이동을 조정하는 SQL 풀의 데이터 전송 기술입니다. 일부 쿼리는 병렬 쿼리가 정확한 결과를 반환하도록 하려면 데이터 이동이 필요합니다. 데이터 이동이 필요할 때 DMS는 올바른 데이터가 올바른 위치에 도달하도록 보장합니다.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>배포

분포는 SQL 풀의 분산 데이터에서 실행되는 병렬 쿼리에 대한 저장소 및 처리의 기본 단위입니다. SQL 풀에서 쿼리를 실행하면 작업이 병렬로 실행되는 60개의 작은 쿼리로 나뉩니다. 

60개의 작은 쿼리는 각각 데이터 분산 중 하나에서 실행됩니다. 각 컴퓨팅 노드는 60개 중 하나 이상의 분산을 관리합니다. 최대 계산 리소스가 있는 SQL 풀에는 Compute 노드당 하나의 분포가 있습니다. 계산 리소스가 최소인 SQL 풀에는 하나의 계산 노드에 대한 모든 분포가 있습니다. 

## <a name="hash-distributed-tables"></a>해시 분산 테이블
해시 분산 테이블은 대형 테이블의 조인 및 집계에 대해 가장 높은 쿼리 성능을 제공할 수 있습니다. 

데이터를 해시 분산 테이블로 샤드하기 위해 SQL 풀은 해시 함수를 사용하여 각 행을 하나의 배포에 결정적으로 할당합니다. 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다. 

![분산 테이블](media//overview-architecture/hash-distributed-table.png "분산 테이블") 

* 각 행은 하나의 분산에 속합니다. 
* 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다. 
* 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

분산 열을 선택할 때 고유성, 데이터 기울이기, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블

라운드 로빈 테이블은 만들기 가장 간단한 테이블이며 로드를 위한 준비 테이블로 사용될 때 빠른 성능을 제공합니다.

라운드 로빈 분산 테이블은 데이터를 테이블 전체에 고르게 분산하지만 최적화는 하지 않습니다. 먼저 분산이 무작위로 선택되고 행 버퍼가 순차적으로 분산에 할당됩니다. 데이터는 로드는 라운드 로빈 테이블이 빠르지만 쿼리 성능은 해시 분산 테이블이 더 뛰어난 경우가 많습니다. 라운드 로빈 테이블에 조인하려면 데이터를 다시 바차게 해야 하므로 시간이 더 걸립니다.

## <a name="replicated-tables"></a>복제된 테이블
복제된 테이블은 작은 테이블에 가장 빠른 쿼리 성능을 제공합니다.

복제된 테이블은 각 컴퓨팅 노드에 테이블의 전체 복사본을 캐시합니다. 결과적으로 테이블을 복제하면 조인 또는 집계 전에 컴퓨팅 노드 간에 데이터를 전송하지 않아도 됩니다. 복제된 테이블은 작은 테이블에서 가장 잘 활용됩니다. 추가 저장소가 필요하며 데이터를 작성할 때 발생하는 추가 오버헤드가 있어 큰 테이블을 비실용적입니다. 

아래 다이어그램은 각 계산 노드의 첫 번째 배포에 캐시된 복제된 테이블을 보여 주며 있습니다. 

![복제 테이블](media/overview-architecture/replicated-table.png "복제 테이블") 

## <a name="next-steps"></a>다음 단계

이제 Synapse SQL에 대해 조금 알고 있으므로 [SQL 풀을](../quickstart-create-sql-pool.md) 빠르게 만들고 샘플 데이터(./sql-data-warehouse-load-sample-databases.md)를 [로드하는](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) 방법을 알아봅니다. 또는 [주문형 SQL을 사용하기](../quickstart-sql-on-demand.md)시작합니다. Azure을 처음 접하는 경우 새 용어를 발견하면 [Azure 용어집](../../azure-glossary-cloud-terminology.md) 을 유용하게 사용할 수 있습니다. 
