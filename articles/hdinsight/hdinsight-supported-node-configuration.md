---
title: Azure HDInsight 지원 노드 구성
description: HDInsight 클러스터 노드에 대 한 최소 및 권장 구성에 대해 알아봅니다.
keywords: vm 크기, 클러스터 크기, 클러스터 구성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484789"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight에 대 한 기본 및 권장 노드 구성은 무엇 인가요?

이 문서에서는 Azure HDInsight 클러스터의 기본 및 권장 노드 구성에 대해 설명 합니다.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터에 대 한 기본 및 최소 권장 노드 구성 및 가상 머신 크기

다음 표에는 HDInsight 클러스터에 대 한 기본 및 권장 VM (가상 머신) 크기가 나와 있습니다.  이 정보는 HDInsight 클러스터를 배포 하기 위해 PowerShell 또는 Azure CLI 스크립트를 만들 때 사용할 VM 크기를 이해 하는 데 필요 합니다.

클러스터에서 32 개 이상의 작업자 노드를 필요로 하는 경우 최소 8 개의 코어와 14gb RAM을 가진 헤드 노드 크기를 선택 합니다. 

데이터 디스크가 있는 유일한 클러스터 유형은 Kafka 고 가속화 된 쓰기 기능이 설정 된 HBase 클러스터입니다. HDInsight는 이러한 시나리오에서 P30 및 S30 디스크 크기를 지원 합니다.

이 문서에 사용 된 최소 권장 VM 유형의 사양은 다음 표에 요약 되어 있습니다.

| 크기              | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

각 VM 유형의 사양에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [범용 가상 머신 크기: Dv2 시리즈 1-5](../virtual-machines/dv2-dsv2-series.md)
* [메모리 액세스에 최적화 된 가상 머신 크기: Dv2 시리즈 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [범용 가상 머신 크기: Av2 시리즈 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>브라질 남부 및 일본 서 부를 제외한 모든 지원 되는 지역

> [!Note]
> Powershell 및 기타 스크립트에서 사용할 SKU 식별자를 가져오려면 아래 표에 있는 모든 VM Sku의 시작 부분에 `Standard_`을 추가 합니다. 예를 들어 `D12_v2` `Standard_D12_v2`됩니다.

| 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 헤드: 기본 VM 크기 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head: 권장 되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| 작업자: 기본 VM 크기 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | broker 당 S30 디스크 2 개를 사용 하는 D12_v2 4 개 |
| 작업자: 권장 되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper: 기본 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| 사육 사: 최소 권장 VM 크기 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 서비스: 기본 VM 크기 |  |  |  |  |  | D4_v2 |  |
| ML 서비스: 권장 되는 최소 VM 크기 |  |  |  |  |  | D4_v2 |  |

\* = Spark Enterprise Security Package (ESP) 클러스터에 대 한 VM 크기

### <a name="brazil-south-and-japan-west-only"></a>브라질 남부 및 일본 서 부만

| 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 | Storm | Spark | ML 서비스 |
|---|---|---|---|---|---|---|
| 헤드: 기본 VM 크기 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: 권장 되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| 작업자: 기본 VM 크기 | D4 | D4 | D14 | D3 | D13 | D4 |
| 작업자: 권장 되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper: 기본 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| 사육 사: 최소 권장 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML 서비스: 기본 VM 크기 |  |  |  |  |  | D4 |
| ML 서비스: 권장 되는 최소 VM 크기 |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - 헤드는 Storm 클러스터 유형에 대한 *Nimbus*로 알려져 있습니다.
> - 작업자는 Storm 클러스터 형식에 대한 *감독자*로 알려져 있습니다.
> - 작업자는 HBase 클러스터 형식에 대한 *지역*으로 알려져 있습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전은?](hdinsight-component-versioning.md)
