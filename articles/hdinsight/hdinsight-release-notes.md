---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/02/2021
ms.openlocfilehash: 61b32852de0cb15db3860b72561f87e56db8f40d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903854"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

릴리스 정보를 구독하려면 [이 GitHub 리포지토리](https://github.com/hdinsight/release-notes/releases)에서 릴리스를 확인하세요.


## <a name="release-date-06022021"></a>릴리스 날짜: 2021/06/02

이 릴리스는 HDInsight 3.6 및 4.0 모두에 적용됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래의 변경 내용이 표시되지 않으면 며칠 후에 해당 지역에서 릴리스가 라이브될 때까지 기다려주세요.

이 릴리스의 OS 버전은 다음과 같습니다.
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>새로운 기능
### <a name="os-version-upgrade"></a>OS 버전 업그레이드
[Ubuntu의 릴리스 주기](https://ubuntu.com/about/release-cycle)에서 언급된 대로 Ubuntu 16.04 커널은 2021년 4월에 EOL(수명 종료)에 도달합니다. 이 리릴스에서는 Ubuntu 18.04에서 실행되는 새 HDInsight 4.0 클러스터 이미지 출시를 시작했습니다. 새로 만든 HDInsight 4.0 클러스터는 제공된 후 기본적으로 Ubuntu 18.04에서 실행됩니다. Ubuntu 16.04의 기존 클러스터는 완벽한 지원과 함께 있는 그대로 실행됩니다.

HDInsight 3.6은 Ubuntu 16.04에서 계속 실행됩니다. 2021년 7월 1일부터 기본 지원(표준 지원에서)으로 변경됩니다. 날짜 및 지원 옵션에 관한 자세한 내용은 [Azure HDInsight 버전](./hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요. HDInsight 3.6에서는 Ubuntu 18.04가 지원되지 않습니다. Ubuntu 18.04를 사용하려면 클러스터를 HDInsight 4.0으로 마이그레이션해야 합니다. 

기존 HDInsight 4.0 클러스터를 Ubuntu 18.04로 이동하려면 클러스터를 삭제하고 다시 만들어야 합니다. Ubuntu 18.04 지원이 제공된 후 클러스터를 만들거나 다시 만들 계획입니다.

새 클러스터를 만든 후 클러스터에 SSH를 실행하고 `sudo lsb_release -a`를 실행하여 Ubuntu 18.04에서 실행되는지 확인할 수 있습니다. 프로덕션으로 이동하기 전에 먼저 테스트 구독에서 애플리케이션을 테스트하는 것이 좋습니다. [HDInsight Ubuntu 18.04 업데이트에 대해 자세히 알아봅니다](./hdinsight-ubuntu-1804-qa.md).

### <a name="scaling-optimizations-on-hbase-accelerated-writes-clusters"></a>HBase 가속 쓰기 클러스터에서 최적화 크기 조정
HDInsight는 HBase 가속 쓰기 지원 클러스터의 크기 조정에 대해 몇 가지 개선 및 최적화를 수행했습니다. [HBase 가속 쓰기에 대해 자세히 알아봅니다](./hbase/apache-hbase-accelerated-writes.md).

## <a name="deprecation"></a>사용 중단
이 릴리스에서는 사용 중단되지 않습니다.

## <a name="behavior-changes"></a>동작 변경
### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsight-40"></a>HDInsight 4.0의 헤드 노드로 Stardard_A5 VM 크기 사용 안 함
HDInsight 클러스터 헤드 노드는 클러스터 초기화 및 관리를 처리합니다. Standard_A5 VM 크기는 HDInsight 4.0의 헤드 노드로서 안정성 문제가 있습니다. 이 릴리스부터 고객은 Standard_A5 VM 크기를 헤드 노드로 사용하여 새 클러스터를 만들 수 없습니다. E2_v3 또는 E2s_v3 같은 다른 2코어 VM을 사용할 수 있습니다. 기존 클러스터는 계속 운영됩니다. 프로덕션 HDInsight 클러스터의 고가용성 및 안정성을 보장하기 위해 헤드 노드에는 4코어 VM을 사용하는 것이 좋습니다.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets에서 실행되는 클러스터에 대한 네트워크 인터페이스 리소스가 표시되지 않음
HDInsight는 Azure Virtual Machine Scale Sets로 점진적으로 마이그레이션되고 있습니다. 가상 머신의 네트워크 인터페이스는 Azure Virtual Machine Scale Sets를 사용하는 클러스터의 고객에게는 더 이상 표시되지 않습니다.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query는 일정 기반 자동 스케일링만 지원합니다.

고객 시나리오가 점점 더 확장되고 다양해짐에 따라 Interactive Query(LLAP) 부하 기반 자동 스케일링에 대한 몇 가지 제한 사항을 확인했습니다. 이 제한 사항은 LLAP 쿼리 역학의 특징, 향후 부하 예측 정확도 문제 및 LLAP 스케줄러의 작업 재배포 문제 때문에 발생합니다. 이 제한 사항으로 인해 사용자는 자동 스케일링이 사용되는 경우 LLAP 클러스터에서 쿼리 실행 속도가 느려지는 것을 알 수 있습니다. 성능에 미치는 영향이 자동 스케일링의 비용 이점보다 클 수 있습니다.

2021년 7월부터 HDInsight의 Interactive Query 워크로드는 일정 기반 자동 스케일링만 지원합니다. 새 Interactive Query 클러스터에서는 더 이상 자동 스케일링을 사용하도록 설정할 수 없습니다. 기존에 실행 중인 클러스터는 위에서 설명된 알려진 제한 사항을 적용하여 계속 실행할 수 있습니다. 

LLAP를 위해 일정 기반 자동 스케일링으로 이동하는 것이 좋습니다.  Grafana Hive 대시보드를 통해 클러스터의 현재 사용 패턴을 분석할 수 있습니다. 자세한 내용은 [Azure HDInsight 클러스터 자동 크기 조정](hdinsight-autoscale-clusters.md)을 참조하세요. 

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>2021년 7월 1일부터 HDInsight 3.6 기본 지원
2021년 7월 1일부터 Microsoft는 특정 HDInsight 3.6 클러스터 유형에 관한 [기본 지원](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)을 제공합니다. 기본 지원 플랜은 2022년 4월 3일까지 이용할 수 있습니다. 2021년 7월 1일부터 기본 지원에 자동으로 등록됩니다. 옵트인하기 위해 수행할 작업은 없습니다. 기본 지원 아래에 클러스터 유형이 포함된 [설명서](hdinsight-36-component-versioning.md)를 참조하세요. 

기존 3.6 환경에 관한 변경 내용을 중지하고 HDInsight 3.6에 새로운 솔루션을 빌드하지 않는 것이 좋습니다. [클러스터를 HDInsight 4.0으로 마이그레이션](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)하는 것이 좋습니다. [HDInsight 4.0의 새로운 기능](hdinsight-version-release.md#whats-new-in-hdinsight-40)에 관해 자세히 알아보세요.

### <a name="vm-host-naming-will-be-changed-on-july-1-2021"></a>VM 호스트 이름은 2021년 7월 1일에 변경됩니다.
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 서비스는 [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md)로 점진적으로 마이그레이션됩니다. 이 마이그레이션은 클러스터 호스트 이름 FQDN 이름 형식을 변경하며 호스트 이름의 숫자는 순서대로 보장되지 않습니다. 각 노드에 대한 FQDN 이름을 가져오려면 [클러스터 노드의 호스트 이름 찾기](./find-host-name.md)를 참조하세요.

### <a name="move-to-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합으로 이동
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 서비스는 [Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)으로 점진적으로 마이그레이션됩니다. 전체 프로세스에는 몇 개월이 걸릴 수 있습니다. 지역 및 구독이 마이그레이션된 후에는 새로 생성된 HDInsight 클러스터가 고객의 작업 없이도 Virtual Machine Scale Sets에서 실행됩니다. 호환성이 손상되는 변경 사항은 없을 것으로 예상됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
[이 문서](./hdinsight-component-versioning.md)에서 HDInsight 4.0 및 HDInsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.
