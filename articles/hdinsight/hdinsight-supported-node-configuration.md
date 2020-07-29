---
title: Azure HDInsight 지원 노드 구성
description: HDInsight 클러스터 노드의 최소 및 권장 구성에 대해 알아봅니다.
keywords: VM 크기, 클러스터 크기, 클러스터 구성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: 0bf5559590b66400fc4fc4dc27ea88c3522effb1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646625"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight의 기본 및 권장 노드 구성은 무엇인가요?

이 문서에서는 Azure HDInsight 클러스터의 기본 및 권장 노드 구성에 대해 설명합니다.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터의 기본 및 최소 권장 노드 구성 및 가상 머신 크기

다음 표에는 HDInsight 클러스터의 기본 및 권장 VM(가상 머신) 크기가 나와 있습니다.  이 정보는 PowerShell 또는 Azure CLI 스크립트를 만들어 HDInsight 클러스터를 배포할 때 사용할 VM 크기를 파악하는 데 필요합니다.

클러스터에 필요한 작업자 노드 수가 32개를 초과하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택합니다.

데이터 디스크가 있는 유일한 클러스터 유형은 Kafka 및 가속화된 쓰기 기능을 사용하도록 설정한 HBase 클러스터입니다. HDInsight는 해당 시나리오에서 P30 및 S30 디스크 크기를 지원합니다. 다른 모든 클러스터 형식의 경우 HDInsight는 클러스터에 관리 디스크 공간을 제공합니다. 2019년 11월 7일부터 새로 만든 클러스터에서 각 노드의 관리 디스크 크기는 128GB입니다. 이는 변경할 수 없습니다.

이 문서에 사용된 모든 최소 권장 VM 유형의 사양은 다음 표에 요약되어 있습니다.

| 크기              | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32            / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

각 VM 유형의 사양에 대한 자세한 내용은 다음 문서를 참조하세요.

* [범용 가상 머신 크기: Dv2 시리즈 1-5](../virtual-machines/dv2-dsv2-series.md)
* [메모리 최적화 가상 머신 크기: Dv2 시리즈 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [범용 가상 머신 크기: Av2 시리즈 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>브라질 남부 및 일본 서부를 제외하고 지원되는 모든 지역:

> [!Note]
> Powershell 및 기타 스크립트에서 사용할 SKU 식별자를 가져오려면 아래 표에 있는 모든 VM SKU의 시작 부분에 `Standard_`를 추가합니다. 예를 들어 `D12_v2`는 `Standard_D12_v2`가 됩니다.

| 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 헤드: 기본 VM 크기 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| 헤드: 권장되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| 작업자: 기본 VM 크기 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4개 D12_v2(브로커당 2개 S30 디스크 포함) |
| 작업자: 권장되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper: 기본 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| Zookeeper: 권장되는 최소 VM 크기 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 서비스: 기본 VM 크기 |  |  |  |  |  | D4_v2 |  |
| ML 서비스: 권장되는 최소 VM 크기 |  |  |  |  |  | D4_v2 |  |

\* = Spark ESP(Enterprise Security Package) 클러스터에 대한 VM 크기

### <a name="brazil-south-and-japan-west-only"></a>브라질 남부 및 일본 서부만 해당

| 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 | Storm | Spark | ML 서비스 |
|---|---|---|---|---|---|---|
| 헤드: 기본 VM 크기 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| 헤드: 권장되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| 작업자: 기본 VM 크기 | D4 | D4 | D14 | D3 | D13 | D4 |
| 작업자: 권장되는 최소 VM 크기 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper: 기본 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Zookeeper: 권장되는 최소 VM 크기 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML 서비스: 기본 VM 크기 |  |  |  |  |  | D4 |
| ML 서비스: 권장되는 최소 VM 크기 |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - 헤드는 Storm 클러스터 유형에 대한 *Nimbus*로 알려져 있습니다.
> - 작업자는 Storm 클러스터 형식에 대한 *감독자*로 알려져 있습니다.
> - 작업자는 HBase 클러스터 형식에 대한 *지역*으로 알려져 있습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전은?](hdinsight-component-versioning.md)
