---
title: Azure HDInsight 클러스터에 적합 한 VM 크기를 선택 하는 방법
description: HDInsight 클러스터에 적합 한 VM 크기를 선택 하는 방법을 알아봅니다.
keywords: VM 크기, 클러스터 크기, 클러스터 구성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: d347707b0f48314dd872bc3ad34ac624817d2937
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535317"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Azure HDInsight 클러스터에 적합 한 VM 크기 선택

이 문서에서는 HDInsight 클러스터의 다양 한 노드에 대 한 올바른 VM 크기를 선택 하는 방법을 설명 합니다. 

먼저 CPU 처리, RAM 크기 및 네트워크 대기 시간과 같은 가상 컴퓨터의 속성이 워크 로드 처리에 미치는 영향을 이해 하는 것으로 시작 합니다. 다음으로 응용 프로그램 및이 응용 프로그램이 다른 VM 제품군에 대해 최적화 된 방법과 어떻게 일치 하는지 생각해 봅니다. 사용할 VM 제품군이 배포 하려는 클러스터 유형과 호환 되는지 확인 합니다 (예: 각 클러스터 유형에 대해 지원 되는 모든 권장 VM 크기 목록은 [Azure HDInsight 지원 노드 구성](hdinsight-supported-node-configuration.md)을 참조 하세요. 마지막으로, 벤치마킹 프로세스를 사용 하 여 일부 샘플 워크 로드를 테스트 하 고 해당 제품군 내에서 적합 한 SKU를 확인할 수 있습니다.

저장소 유형 또는 클러스터 크기를 선택 하는 등 클러스터의 다른 측면을 계획 하는 방법에 대 한 자세한 내용은 [HDInsight 클러스터에 대 한 용량 계획](hdinsight-capacity-planning.md)을 참조 하세요.

## <a name="vm-properties-and-big-data-workloads"></a>VM 속성 및 빅 데이터 워크 로드

VM 크기와 유형은 CPU 처리 성능, RAM 크기 및 네트워크 대기 시간에 따라 결정됩니다.

- CPU: VM 크기는 코어 수를 나타냅니다. 코어 수가 많을수록 각 노드가 달성할 수 있는 병렬 계산 수준이 높아집니다. 또한 일부 VM 유형은 더 빠른 코어를 포함합니다.

- RAM: VM 크기는 VM에서 사용할 수 있는 RAM의 양을 나타내기도 합니다. 데이터를 디스크에서 읽지 않고, 처리를 위해 데이터를 메모리에 저장하는 워크로드의 경우, 작업자 노드에 데이터에 맞는 충분한 메모리가 있는지 확인합니다.

- 네트워크: 대부분의 클러스터 유형에 대해 클러스터에서 처리 하는 데이터는 로컬 디스크에 있지 않고 Data Lake Storage 또는 Azure Storage 같은 외부 저장소 서비스에 저장 됩니다. 노드 VM 및 스토리지 서비스 간의 네트워크 대역폭과 처리량을 고려합니다. VM에 사용할 수 있는 네트워크 대역폭은 일반적으로 더 크게 늘어납니다. 자세한 내용은 [VM 크기 개요](../virtual-machines/sizes.md)를 참조하세요.

## <a name="understanding-vm-optimization"></a>VM 최적화 이해

Azure의 가상 머신 제품군은 다양 한 사용 사례에 맞게 최적화 됩니다. 아래 표에서 가장 인기 있는 사용 사례와 일치 하는 VM 제품군 중 일부를 찾을 수 있습니다.

| Type                     | 크기           |    설명       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [항목 수준](../virtual-machines/sizes-general.md)          | A, Av2  | 개발 및 테스트와 같은 항목 수준 작업에 가장 적합 한 CPU 성능 및 메모리 구성이 있습니다. 경제적 이며 Azure를 시작 하기 위한 저렴 한 옵션을 제공 합니다. |
| [범용](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | CPU 대 메모리 비율이 적당합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md)        | F           | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 애플리케이션 서버에 적합합니다.        |
| [메모리에 최적화](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | 메모리 대 CPU 비율이 높습니다. 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |

- HDInsight 지원 지역에서 사용 가능한 VM 인스턴스의 가격 책정에 대 한 자세한 내용은 [Hdinsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조 하세요.

## <a name="cost-saving-vm-types-for-light-workloads"></a>간단한 워크 로드에 대 한 VM 유형 저장 비용

처리 요구 사항이 적은 경우 [F 시리즈](https://azure.microsoft.com/blog/f-series-vm-size/) 를 사용 하 여 HDInsight를 시작할 수 있습니다. 시간당 가격이 더 낮은 F 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

다음 표에서는 Fsv2 시리즈 Vm을 사용 하 여 만들 수 있는 클러스터 유형 및 노드 유형을 설명 합니다.

| 클러스터 유형 | Version | 작업자 노드 | 헤드 노드 | 사육 아웃 노드 |
|---|---|---|---|---|
| Spark | 모두 | F4 이상 | 아니요 | 아니요 |
| Hadoop은 | 모두 | F4 이상 | 아니요 | 아니요 |
| Kafka | 모두 | F4 이상 | 아니요 | 아니요 |
| HBase | 모두 | F4 이상 | 아니요 | 아니요 |
| LLAP | disabled | 아니요 | 아니요 | 아니요 |
| Storm | disabled | 아니요 | 아니요 | 아니요 |
| ML 서비스 | HDI 3.6만 해당 | F4 이상 | 아니요 | 아니요 |

각 F 시리즈 SKU의 사양을 보려면 [f 시리즈 VM 크기](https://azure.microsoft.com/blog/f-series-vm-size/)를 참조 하세요.

## <a name="benchmarking"></a>벤치마킹

벤치마킹은 다른 Vm에서 시뮬레이션 된 워크 로드를 실행 하 여 프로덕션 워크 로드에 얼마나 잘 수행 되는지 측정 하는 프로세스입니다. 

VM Sku 및 클러스터 크기에 대 한 벤치마킹에 대 한 자세한 내용은 [Azure HDInsight의 클러스터 용량 계획 ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure HDInsight 지원 노드 구성](hdinsight-supported-node-configuration.md)
- [Azure에서 Linux 가상 머신에 대한 크기](../virtual-machines/sizes.md)