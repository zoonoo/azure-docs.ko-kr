---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop 구성 요소 및 버전에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124655"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Azure HDInsight에서 사용할 수 있는 Apache 구성 요소 및 버전

이 문서에서는 Azure HDInsight 및 Enterprise Security Package에서 [Apache Hadoop](https://hadoop.apache.org/) 환경 구성 요소 및 버전에 대해 알아봅니다. 또한 HDInsight에서 Hadoop 구성 요소 버전을 확인 하는 방법을 알아봅니다.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>다른 HDInsight 버전에서 사용할 수 있는 Apache 구성 요소

Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 2017 4 월 4 일, Azure HDInsight에서 사용 하는 기본 클러스터 버전은 3.6입니다.

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 나열되어 있습니다.

> [!NOTE]
> HDInsight 서비스의 기본 버전은 예고 없이 변경될 수 있습니다. 버전 종속성이 있는 경우 Azure PowerShell 및 Azure 클래식 CLI를 사용 하 여 .NET SDK를 사용 하 여 클러스터를 만들 때 HDInsight 버전을 지정 합니다.

| 구성 요소              | HDInsight 4.0 | HDInsight 3.6(기본값)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 및 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP 대화형 쿼리) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (참고 참조)   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 시스템 성능 고려 사항으로 인해 3 월 2019에 Kafka 버전 0.10에 대 한 지원이 만료 되었습니다.

## <a name="check-for-current-apache-component-version-information"></a>현재 Apache 구성 요소 버전 정보 확인

HDInsight 클러스터 버전과 연결 된 Hadoop 환경 구성 요소 버전은 HDInsight에 대 한 업데이트로 변경 될 수 있습니다. Hadoop 구성 요소를 확인하고 클러스터에 대해 사용 중인 버전을 확인하려면 Ambari REST API를 사용합니다. **GetComponentInformation** 명령은 서비스 구성 요소에 대한 정보를 검색합니다. 자세한 내용은 [Apache Ambari 설명서](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 참조 하세요.

### <a name="release-notes"></a>릴리스 정보

최신 버전의 HDInsight에 대 한 추가 릴리스 정보는 [hdinsight 릴리스 정보](hdinsight-release-notes.md)를 참조 하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 버전에 대 한 만료 및 사용 중지 지원

**지원 만료** 는 Microsoft에서 더 이상 특정 HDInsight 버전에 대 한 지원을 제공 하지 않음을 의미 합니다. 클러스터를 만들기 위해 Azure Portal를 통해 더 이상 사용할 수 없습니다. 이러한 버전은 Azure CLI 또는 다양 한 Sdk를 사용 하 여 계속 만들 수 있습니다.

사용 중지는 HDInsight 버전의 기존 클러스터가 계속 해 서 그대로 실행 됨 **을 의미 합니다** . 이 버전의 새 클러스터는 CLI 및 Sdk를 포함 하는 어떤 방법으로도 만들 수 없습니다. 수동 크기 조정 및 자동 크기 조정과 같은 다른 제어 평면 기능도 버전 사용 중지 후 작동 하지 않을 수 있습니다. 사용 중지 된 버전에는 지원을 사용할 수 없습니다.

다음 표에서는 HDInsight 버전을 보여줍니다. 지원 만료 날짜와 사용 중지 날짜도 알려져 있는 경우에도 제공 됩니다.

### <a name="available-versions"></a>사용 가능한 버전

이 표에는 Azure Portal에서 사용할 수 있는 HDInsight 버전 및 PowerShell 및 .NET SDK와 같은 다른 배포 방법을 통해 나와 있습니다.

| HDInsight 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에서 사용 가능 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018년 9월 24일 | | |예 |예 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017년 4월 4일 | 2020 년 12 월 31 일 |2020 년 12 월 31 일 |예 |예 |

Spark 2.1, 2.2 및 Kafka 1.0 지원은 2020 년 6 월 30 일에 만료 됩니다.

> [!NOTE]
> 버전에 대 한 지원이 만료 되 면 Azure Portal를 통해 사용 하지 못할 수 있습니다. 클러스터 버전은 Windows PowerShell [AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 명령에서 **version** 매개 변수를 사용 하 고 버전 사용 중지 날짜까지 .net SDK를 사용 하 여 계속 사용할 수 있습니다.

### <a name="retired-versions"></a>사용 중지된 버전

이 표에는 Azure Portal에서 사용할 수 없는 HDInsight 버전이 나열 되어 있습니다.

| HDInsight 버전 | HDP 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에서 사용 가능 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016년 9월 30일 |2017년 9월 5일 |2018년 6월 28일 |예 |아니요 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016년 3월 29일 |2016년 12월 29일 |2018년 1월 9일 |예 |아니요 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015년 12월 2일 |2016년 6월 27일 |2018년 7월 31일 |예 |아니요 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015년 12월 2일 |2016년 6월 27일 |2017년 7월 31일 |예 |아니요 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 또는 Windows Server 2012 R2 |2015년 2월 18일 |2016년 3월 1일 |2017년 4월 1일 |예 |아니요 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014년 6월 24일 |2015년 5월 18일 |2016년 6월 30일 |예 |아니요 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014년 2월 11일 |2014년 9월 17일 |2015년 6월 30일 |예 |아니요 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013년 10월 28일 |2014년 5월 12일 |2015년 5월 31일 |예 |아니요 |
| HDInsight 1.6 |HDP 1.1 | |2013년 10월 28일 |2014년 4월 26일 |2015년 5월 31일 |아니요 |아니요 |

> [!NOTE]
> 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 버전 2.1 이상에 배포됩니다. HDInsight 버전 1.6 클러스터에서는 사용할 수 없습니다.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 클러스터 버전에 대 한 서비스 수준 계약

서비스 수준 계약은 _지원 창_으로 정의 됩니다. 지원 기간은 Microsoft 고객 서비스 및 지원에서 HDInsight 버전이 지원 되는 기간입니다. 버전에서 _지원 만료 날짜가_경과 된 경우 HDInsight 클러스터는 지원 기간을 벗어납니다. HDInsight 버전 X에 대 한 지원 만료 (최신 X + 1 버전을 사용할 수 있는 경우)는 이후 버전입니다.

- **수식 1:** HDInsight 클러스터 버전 X가 릴리스된 날짜에 180 일을 추가 합니다.
- **수식 2:** Azure Portal에서 HDInsight 클러스터 버전 X + 1을 사용할 수 있게 된 날짜에 90 일을 추가 합니다.

사용 중지 _날짜_ 는 HDInsight에서 클러스터 버전을 만들 수 없는 날짜입니다. 2017 년 7 월 31 일부 터 사용 중지 날짜 이후에는 HDInsight 클러스터의 크기를 조정할 수 없습니다.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터에 대한 기본 노드 구성 및 가상 머신 크기

클러스터에 대해 선택할 가상 컴퓨터 Sku에 대 한 자세한 내용은 [Azure HDInsight 클러스터 구성 세부 정보](hdinsight-supported-node-configuration.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
- [Azure HDInsight 버전과 관련 된 Hortonworks 릴리스 정보](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)