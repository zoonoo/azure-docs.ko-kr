---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607221"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

릴리스 정보를 구독 하려는 경우 [이 GitHub 리포지토리에서](https://github.com/hdinsight/release-notes/releases)릴리스를 시청 하세요.

## <a name="release-date-03242021"></a>릴리스 날짜: 03/24/2021

이 릴리스는 HDInsight 3.6 및 HDInsight 4.0 모두에 적용 됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새로운 기능
### <a name="spark-30-preview"></a>Spark 3.0 미리 보기
HDInsight는 HDInsight 4.0에 [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) 지원 기능을 미리 보기 기능으로 추가 했습니다. 

### <a name="kafka-24-preview"></a>Kafka 2.4 미리 보기
HDInsight는 HDInsight 4.0에 [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) 지원을 미리 보기 기능으로 추가 했습니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 서비스는 [Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)으로 점진적으로 마이그레이션됩니다. 전체 프로세스에는 몇 개월이 걸릴 수 있습니다. 지역 및 구독이 마이그레이션된 후에는 새로 만든 HDInsight 클러스터가 고객의 작업 없이도 가상 머신 확장 집합에서 실행 됩니다. 주요 변경 내용은 필요 하지 않습니다.

## <a name="deprecation"></a>사용 중단
이 릴리스에서는 사용 중단 되지 않습니다.

## <a name="behavior-changes"></a>동작 변경
### <a name="default-cluster-version-is-changed-to-40"></a>기본 클러스터 버전이 4.0로 변경 됨
HDInsight 클러스터의 기본 버전은 3.6에서 4.0로 변경 됩니다. 사용 가능한 버전에 대 한 자세한 내용은 [사용 가능한 버전](./hdinsight-component-versioning.md)을 참조 하세요. [HDInsight 4.0](./hdinsight-version-release.md)의 새로운 기능에 대해 자세히 알아보세요.

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>기본 클러스터 VM 크기가 Ev3 시리즈로 변경 됨 
기본 클러스터 VM 크기는 D 시리즈에서 Ev3 시리즈로 변경 됩니다. 이 변경 내용은 헤드 노드 및 작업자 노드에 적용 됩니다. 이러한 변경을 테스트 된 워크플로에 영향을 주지 않도록 하려면 ARM 템플릿에서 사용할 VM 크기를 지정 합니다.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합에서 실행 되는 클러스터에 대 한 네트워크 인터페이스 리소스가 표시 되지 않음
HDInsight는 Azure 가상 머신 확장 집합으로 점진적으로 마이그레이션됩니다. 가상 컴퓨터의 네트워크 인터페이스는 Azure virtual machine scale sets를 사용 하는 클러스터의 고객에 게 더 이상 표시 되지 않습니다.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="os-version-upgrade"></a>OS 버전 업그레이드
HDInsight는 Ubuntu 16.04에서 18.04로 OS 버전을 업그레이드 합니다. 업그레이드는 4 월 2021 일 전에 완료 됩니다.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 6 월 30 2021의 지원 종료
HDInsight 3.6은 지원 종료 될 예정입니다. 6 월 30 2021 양식을 시작 하면 고객이 새 HDInsight 3.6 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지 하려면 HDInsight 4.0로 이동 하는 것이 좋습니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
Spark 3.0.0 및 Kafka 2.4.1에 대 한 지원이 미리 보기로 추가 되었습니다. [이 문서](./hdinsight-component-versioning.md)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.
