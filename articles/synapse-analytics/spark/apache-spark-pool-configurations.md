---
title: Apache Spark 풀 개념
description: Azure Synapse Analytics의 Apache Spark 풀 크기 및 구성에 대 한 소개입니다.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606160"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Apache Spark 풀 구성

Spark 풀은 Spark 인스턴스가 인스턴스화될 때 계산 리소스 요구 사항 및 관련 동작 특성을 정의 하는 메타 데이터 집합입니다. 이러한 특성에는 이름, 노드 수, 노드 크기, 크기 조정 동작 및 ttl (time to live)이 포함 되지만이에 국한 되지 않습니다. 자체의 Spark 풀은 리소스를 사용 하지 않습니다. Spark 풀을 만들 때 발생 하는 비용은 없습니다. 요금은 대상 Spark 풀에서 Spark 작업이 실행 되 고 Spark 인스턴스가 요청 시 인스턴스화되는 경우에만 발생 합니다.

[Synapse Analytics에서 Spark 풀 시작](../quickstart-create-apache-spark-pool-portal.md)에서 Spark 풀을 만드는 방법과 모든 속성을 확인할 수 있습니다.

## <a name="nodes"></a>노드

Apache Spark 풀 인스턴스는 Spark 인스턴스에서 최소 세 개 이상의 노드를 포함 하는 하나의 헤드 노드와 둘 이상의 작업자 노드로 구성 됩니다.  헤드 노드는 Livy, Yarn 리소스 관리자, 사육 사 및 Spark 드라이버와 같은 추가 관리 서비스를 실행 합니다.  모든 노드는 노드 에이전트 및 Yarn Node Manager와 같은 서비스를 실행 합니다. 모든 작업자 노드는 Spark Executor 서비스를 실행 합니다.

## <a name="node-sizes"></a>노드 크기

Spark 풀은 XXLarge 계산 432 64 노드 8 vCore 및 64 g b의 메모리를 포함 하는 작은 계산 노드의 범위에 해당 하는 노드 크기를 사용 하 여 정의할 수 있습니다. 인스턴스를 다시 시작 해야 할 수도 있지만 풀을 만든 후 노드 크기를 변경할 수 있습니다.

|크기 | vCore | 메모리|
|-----|------|-------|
|Small|4|32GB|
|중간|8|64GB|
|대형|16|128GB|
|XLarge|32|256GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>자동 크기 조정

Apache Spark 풀은 활동의 양에 따라 계산 리소스를 자동으로 확장 및 축소할 수 있는 기능을 제공 합니다.  자동 크기 조정 기능을 사용 하는 경우 확장할 노드의 최소 및 최대 수를 설정할 수 있습니다.
자동 크기 조정 기능이 사용 하지 않도록 설정 된 경우 설정 된 노드 수는 고정 된 상태로 유지 됩니다.  이 설정은 인스턴스를 다시 시작 해야 할 수도 있지만 풀을 만든 후에 변경할 수 있습니다.

## <a name="automatic-pause"></a>자동 일시 중지

자동 일시 중지 기능은 설정 된 유휴 기간 후 리소스를 해제 하 여 Apache Spark 풀의 전체 비용을 줄입니다.  이 기능을 사용 하도록 설정 하면 유휴 시간 (분)을 설정할 수 있습니다.  자동 일시 중지 기능은 자동 크기 조정 기능과는 독립적입니다. 자동 크기 조정을 사용 하거나 사용 하지 않도록 설정 했는지 여부에 관계 없이 리소스를 일시 중지할 수 있습니다.  이 설정은 인스턴스를 다시 시작 해야 할 수도 있지만 풀을 만든 후에 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark 설명서](https://spark.apache.org/docs/2.4.5/)