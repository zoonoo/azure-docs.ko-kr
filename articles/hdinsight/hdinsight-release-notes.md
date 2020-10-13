---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974569"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-10082020"></a>릴리스 날짜: 10/08/2020

이 릴리스는 HDInsight 3.6 및 HDInsight 4.0 모두에 적용 됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새로운 기능
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>공용 IP 및 개인 링크가 없는 HDInsight 개인 클러스터 (미리 보기)
이제 HDInsight는 미리 보기에서 클러스터에 대 한 공용 IP 및 개인 링크 액세스 권한이 없는 클러스터 만들기를 지원 합니다. 고객은 새 고급 네트워킹 설정을 사용 하 여 공용 IP가 없는 완전히 격리 된 클러스터를 만들고 자신의 개인 끝점을 사용 하 여 클러스터에 액세스할 수 있습니다. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 버전부터 서비스는 [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)으로 점진적으로 마이그레이션됩니다. 전체 프로세스에는 몇 개월이 걸릴 수 있습니다. 지역 및 구독이 마이그레이션된 후에는 새로 만든 HDInsight 클러스터가 고객의 작업 없이도 가상 머신 확장 집합에서 실행 됩니다. 주요 변경 내용은 필요 하지 않습니다.

## <a name="deprecation"></a>사용 중단
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>HDInsight 3.6 ML 서비스 클러스터의 사용 중단
HDInsight 3.6 ML 서비스 클러스터 유형은 Dec 31 2020에서 지원 종료 됩니다. 이후 고객은 새 3.6 ML 서비스 클러스터를 만들지 않습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)에서 HDInsight 버전 및 클러스터 유형에 대 한 지원 만료를 확인 합니다.

## <a name="behavior-changes"></a>동작 변경 내용
이 릴리스에 대 한 동작은 변경 되지 않습니다.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Spark, Hadoop 및 ML 서비스에 대해 다른 사육 사 가상 머신 크기를 선택 하는 기능
현재 HDInsight는 Spark, Hadoop 및 ML 서비스 클러스터 유형에 대해 아웃 지원팀 노드 크기를 사용자 지정할 수 없습니다. 기본적으로 A2_v2/A2 가상 머신 크기가 무료로 제공 됩니다. 향후 릴리스에서는 시나리오에 가장 적합 한 사육 사 가상 머신 크기를 선택할 수 있습니다. A2_v2/A2 이외의 가상 컴퓨터 크기를 포함 하는 사육 아웃 노드에는 요금이 부과 됩니다. A2_v2 및 A2 가상 머신은 계속 무료로 제공 됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. [이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.