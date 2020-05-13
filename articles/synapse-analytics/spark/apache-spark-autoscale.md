---
title: Azure Synapse Apache Spark 인스턴스 크기 자동 조정
description: Azure Synapse 자동 크기 조정 기능을 사용 하 여 Apache Spark 인스턴스의 크기를 자동으로 조정
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210515"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Azure Synapse Analytics Apache Spark 풀 크기 자동 조정

Azure Synapse Spark 풀의 자동 크기 조정 기능은 클러스터 인스턴스의 노드 수를 자동으로 확장 및 축소 합니다. 새 Azure Synapse Spark 풀을 만드는 동안 자동 크기 조정을 선택 하면 최소 및 최대 노드 수를 설정할 수 있습니다. 자동 크기 조정은 부하의 리소스 요구 사항을 모니터링 하 고 노드 수를 확장 또는 축소 합니다. 이 기능에 대 한 추가 비용은 없습니다.

## <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 Spark 인스턴스를 지속적으로 모니터링 하 고 다음 메트릭을 수집 합니다.

|메트릭|설명|
|---|---|
|총 보류 중인 CPU|모든 보류 중인 노드의 실행을 시작 하는 데 필요한 총 코어 수입니다.|
|총 보류 중인 메모리|모든 보류 중인 노드의 실행을 시작 하는 데 필요한 총 메모리 (MB)입니다.|
|총 사용 가능한 CPU|활성 노드에서 사용 하지 않는 모든 코어의 합계입니다.|
|사용 가능한 총 메모리|활성 노드에서 사용 하지 않는 메모리 (MB)의 합계입니다.|
|노드당 사용 되는 메모리|노드의 로드입니다. 10gb의 메모리가 사용 되는 노드는 사용 중인 메모리가 2gb 인 작업자 보다 더 많은 부하를 받고 있는 것으로 간주 됩니다.|

위의 메트릭은 30 초 마다 확인 됩니다. 자동 크기 조정은 이러한 메트릭에 따라 확장 및 축소 결정을 내립니다.

## <a name="load-based-scale-conditions"></a>부하 기반 크기 조정 조건

다음 조건이 검색 되 면 자동 크기 조정에서 크기 조정 요청을 실행 합니다.

|강화|축소|
|---|---|
|보류 중인 총 CPU가 사용 가능한 총 CPU보다 큰 시간이 1분을 초과합니다.|총 보류 중인 CPU가 2 분 넘게 총 사용 가능한 CPU 보다 낮습니다.|
|보류 중인 총 메모리가 사용 가능한 총 메모리보다 큰 시간이 1분을 초과합니다.|총 보류 중인 메모리가 2 분 넘게 사용 가능한 총 메모리 보다 짧습니다.|

강화를 위해 Azure Synapse 자동 크기 조정 서비스는 현재 CPU 및 메모리 요구 사항을 충족 하는 데 필요한 새 노드 수를 계산한 다음 필요한 노드 수를 추가 하기 위한 확장 요청을 실행 합니다.

실행 기 수, 노드당 응용 프로그램 마스터 및 현재 CPU와 메모리 요구 사항을 기반으로 하는 축소의 경우 자동 크기 조정에서 특정 수의 노드를 제거 하는 요청을 실행 합니다. 또한 서비스는 현재 작업 실행을 기반으로 제거할 노드를 검색 합니다. Scale down 작업은 먼저 노드를 add-on 다음 클러스터에서 제거 합니다.

## <a name="get-started"></a>시작하기

### <a name="create-a-spark-pool-with-autoscaling"></a>자동 크기 조정을 사용 하 여 Spark 풀 만들기

자동 크기 조정 기능을 사용 하려면 일반적인 풀 만들기 프로세스의 일부로 다음 단계를 완료 합니다.

1. **기본 사항** 탭에서 **자동 크기 조정 사용** 확인란을 선택 합니다.
1. 다음 속성에 대해 원하는 값을 입력 합니다.  

    * **최소** 노드 수입니다.
    * **최대** 노드 수입니다.

초기 노드 수는 최소입니다. 이 값은 생성 될 때 인스턴스의 초기 크기를 정의 합니다. 최소 노드 수는 3 개이 하 여야 합니다.

## <a name="best-practices"></a>모범 사례

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>규모 확장 또는 축소 작업의 대기 시간 고려

크기 조정 작업을 완료 하는 데 1 ~ 5 분 정도 걸릴 수 있습니다.

### <a name="preparation-for-scaling-down"></a>축소 준비

인스턴스 규모를 축소 하는 동안 자동 크기 조정 기능은 노드를 서비스 해제 상태로 전환 하므로 해당 노드에서 새 실행 기를 시작할 수 없습니다.

실행 중인 작업은 계속 실행 되 고 완료 됩니다. 보류 중인 작업은 사용 가능한 노드가 적을수록 정상적으로 예약 될 때까지 대기 합니다.

## <a name="next-steps"></a>다음 단계

새 Spark 풀을 설정 하는 빠른 시작 [spark 풀 만들기](..\quickstart-create-apache-spark-pool.md)
