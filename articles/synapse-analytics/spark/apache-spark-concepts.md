---
title: Azure Synapse Analytics의 Apache Spark - 핵심 개념
description: 이 문서에서는 Azure Synapse Analytics의 Apache Spark와 다양한 개념을 소개합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b1db306ffdb1c05c880e5fc639de2cc1db130d8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096285"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics의 Apache Spark 핵심 개념

Apache Spark는 메모리 내 처리를 지원하여 빅 데이터 분석 애플리케이션의 성능을 향상하는 병렬 처리 프레임워크입니다. Azure Synapse Analytics의 Apache Spark는 Microsoft가 구현한 클라우드의 Apache Spark 중 하나입니다. 

Azure Synapse를 사용하면 Azure에서 Spark 기능을 쉽게 만들고 구성할 수 있습니다. Azure Synapse는 여기서 설명하는 Spark 기능을 다른 방법으로 구현합니다.

## <a name="spark-pools-preview"></a>Spark 풀(미리 보기)

Spark 풀(미리 보기)은 Azure Portal에서 만듭니다. 이 풀은 인스턴스화하면 데이터를 처리하는 Spark 인스턴스를 만드는 데 사용되는 Spark 풀의 정의입니다. 생성된 Spark 풀은 메타데이터로만 존재하며, 따라서 리소스가 사용되거나, 실행되거나, 요금이 청구되지 않습니다. Spark 풀에는 Spark 인스턴스의 특성을 제어하는 일련의 속성이 있습니다. 이러한 특성으로는 이름, 크기, 크기 조정 동작, TTL(time-to-live) 등이 포함됩니다(이에 국한되지 않음).

Spark 풀을 만들 때 비용 또는 리소스가 들지 않으므로 원하는 만큼 다양한 구성을 사용하여 원하는 만큼 풀을 만들 수 있습니다. 또한 Spark 풀에 권한을 적용하여 사용자가 일부 풀에만 액세스하도록 설정할 수 있습니다.

가장 좋은 방법은 개발 및 디버깅에 사용할 소규모 Spark 풀을 만든 다음, 프로덕션 워크로드를 실행하기 위한 더 큰 Spark 풀을 만드는 것입니다.

[Synapse Analytics에서 Spark 풀 시작](../quickstart-create-apache-spark-pool.md)에서 Spark 풀을 만드는 방법과 모든 속성을 확인할 수 있습니다.

## <a name="spark-instances"></a>Spark 인스턴스

Spark 인스턴스는 Spark 풀에 연결하고, 세션을 만들고, 작업을 실행할 때 생성됩니다. 여러 사용자가 단일 Spark 풀에 액세스할 수 있기 때문에 연결하는 사용자마다 새 Spark 인스턴스가 만들어집니다. 

두 번째 작업을 제출할 때 풀에 용량이 있으면 기존 Spark 인스턴스도 용량을 갖게 되고 따라서 기존 인스턴스가 작업을 처리합니다. 풀에는 용량이 없고 풀 수준에 용량이 있는 경우에는 새 Spark 인스턴스가 생성됩니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1

- 클러스터 크기가 노드 20개로 고정된 SP1이라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스 J2가 SI1에서 처리됩니다.
- J2에서 11개 노드를 요청했다면 SP1 또는 SI1의 용량이 부족했을 것입니다. 이 경우 J2가 Notebook에서 온다면 작업이 거부되고, J2가 배치 작업에서 온다면 큐에 추가됩니다.

### <a name="example-2"></a>예제 2

- 노드 10–20개 사이에서 자동으로 크기가 조정되는 SP2라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스가 자동으로 노드 20개로 확장되어 J2를 처리합니다.

### <a name="example-3"></a>예제 3

- 클러스터 크기가 노드 20개로 고정된 SP1이라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 또 다른 사용자 U2가 노드 10개를 사용하는 작업 J3를 제출합니다. 그러면 이 작업을 처리하기 위해 새 Spark 인스턴스 SI2가 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스 J2가 SI1에서 처리됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 설명서](https://spark.apache.org/docs/2.4.4/)
