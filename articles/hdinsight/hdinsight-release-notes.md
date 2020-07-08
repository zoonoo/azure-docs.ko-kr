---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564411"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-06112020"></a>릴리스 날짜: 06/11/2020

이 릴리스는 HDInsight 3.6 및 4.0 둘 다에 적용됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새로운 기능
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
HDInsight는 Azure virtual machines를 사용 하 여 클러스터를 지금 프로 비전 합니다. 이 릴리스에서 새로 만든 HDInsight 클러스터는 Azure 가상 머신 확장 집합을 사용 하기 시작 합니다. 변경 내용이 점진적으로 롤아웃 됩니다. 주요 변경 내용은 필요 하지 않습니다. [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)에 대 한 자세한 내용을 참조 하세요.
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>HDInsight 클러스터에서 Vm 다시 부팅
이 릴리스에서는 응답 하지 않는 노드를 다시 부팅 하기 위해 HDInsight 클러스터의 Vm을 다시 부팅 하도록 지원 합니다. 현재는 API를 통해서만이 작업을 수행할 수 있으며, PowerShell 및 CLI 지원은 해당 방식으로 지원 됩니다. API에 대 한 자세한 내용은 [이 문서](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)를 참조 하세요.
 
## <a name="deprecation"></a>사용 중단
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark 클러스터의 Spark 2.1 및 2.2 사용 중단
7 월 1 2020부터 고객은 HDInsight 3.6에서 Spark 2.1 및 2.2을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 3.6의 Spark 2.3으로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 클러스터의 Spark 2.3 사용 중단
7 월 1 2020부터 고객이 HDInsight 4.0의 Spark 2.3을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Spark 2.4로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 클러스터의 Kafka 1.1 사용 중단
2020년 7월 1일부터 고객은 HDInsight 4.0의 Kafka 1.1을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Kafka 2.1로 전환하는 것이 좋습니다.
 
## <a name="behavior-changes"></a>동작 변경 내용
### <a name="esp-spark-cluster-head-node-size-change"></a>ESP Spark 클러스터 헤드 노드 크기 변경 
ESP Spark 클러스터에 허용 되는 최소 헤드 노드 크기는 Standard_D13_V2로 변경 됩니다. 코어 수가 낮거나 메모리를 헤드 노드로 사용 하는 Vm은 CPU와 메모리 용량이 상대적으로 적기 때문에 ESP 클러스터 문제를 일으킬 수 있습니다. 릴리스부터는 Standard_D13_V2 보다 높은 Sku를 사용 하 고 ESP Spark 클러스터의 경우 헤드 노드로 Standard_E16_V3 합니다.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>헤드 노드에는 최소 4코어 VM이 필요합니다. 
HDInsight 클러스터의 고가용성 및 안정성을 보장하기 위해 헤드 노드에는 최소 4코어 VM이 필요합니다. 2020년 4월 6일부터 고객은 새 HDInsight 클러스터에 대한 헤드 노드로 4코어 이상의 VM만 선택할 수 있습니다. 기존 클러스터는 정상적으로 계속 실행됩니다. 
 
### <a name="cluster-worker-node-provisioning-change"></a>클러스터 작업자 노드 프로 비전 변경
작업자 노드의 80%가 준비 되 면 클러스터가 **작동** 단계에 들어갑니다. 이 단계에서 고객은 스크립트 및 작업 실행과 같은 모든 데이터 평면 작업을 수행할 수 있습니다. 하지만 고객은 규모 확장/축소와 같은 제어 평면 작업을 수행할 수 없습니다. 삭제만 지원 됩니다.
 
**운영** 단계 후 클러스터는 남은 20% 작업자 노드에 대해 다른 60 분 동안 기다립니다. 이 60 분이 종료 되 면 모든 작업자 노드를 아직 사용할 수 없더라도 클러스터가 **실행 중인** 단계로 이동 합니다. 클러스터가 **실행 중인** 단계로 들어가면 정상적으로 사용할 수 있습니다. 확장/축소와 같은 제어 계획 작업과 스크립트 및 작업 실행과 같은 데이터 계획 작업은 모두 허용 됩니다. 요청 된 작업자 노드 중 일부를 사용할 수 없는 경우 클러스터가 부분 성공으로 표시 됩니다. 성공적으로 배포 된 노드에 대 한 요금이 청구 됩니다. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>HDInsight를 통해 새 서비스 주체 만들기
이전에는 클러스터를 만들 때 고객이 Azure Portal의 연결 된 ADLS Gen 1 계정에 액세스 하는 새 서비스 주체를 만들 수 있습니다. June 15 2020부터 고객이 HDInsight 만들기 워크플로에 새 서비스 주체를 만들 수 없습니다. 기존 서비스 사용자만 지원 됩니다. [Azure Active Directory를 사용 하 여 서비스 주체 및 인증서 만들기를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)참조 하세요.

### <a name="time-out-for-script-actions-with-cluster-creation"></a>클러스터를 만든 스크립트 작업 시간 제한
HDInsight는 클러스터를 만드는 스크립트 작업 실행을 지원 합니다. 이 릴리스에서 클러스터를 만드는 모든 스크립트 작업은 **60 분**이내에 완료 되어야 합니다. 그렇지 않으면 시간 제한이 초과 됩니다. 실행 중인 클러스터에 제출 된 스크립트 작업에는 영향을 주지 않습니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process)를 참조하세요.
 
## <a name="upcoming-changes"></a>예정된 변경
앞으로 주의 해야 하는 주요 변경 내용이 없습니다.
 
## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 
 
## <a name="component-version-change"></a>구성 요소 버전 변경
### <a name="hbase-20-to-216"></a>HBase 2.0-2.1.6
HBase 버전은 버전 2.0에서 2.1.6로 업그레이드 됩니다.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0-2.4.4
Spark 버전은 2.4.0 버전에서 2.4.4로 업그레이드 됩니다.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0-2.1.1
Kafka 버전은 2.1.0 버전에서 2.1.1로 업그레이드 됩니다.
 
[이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) 에서 hdinsight 3.6 4.0의 최신 구성 요소 버전을 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="hive-warehouse-connector-issue"></a>Hive 웨어하우스 커넥터 문제
이 릴리스에서는 Hive 웨어하우스 커넥터에 문제가 있습니다. 이 픽스는 다음 릴리스에 포함 될 예정입니다. 이 릴리스 이전에 만든 기존 클러스터는 영향을 받지 않습니다. 가능 하면 droping을 사용 하지 않고 클러스터를 다시 만들어야 합니다. 이에 대 한 추가 도움말이 필요한 경우 지원 티켓을 여세요.
