---
title: Apache Hadoop 구성 요소 및 버전-Azure HDInsight 3.6
description: Azure HDInsight 3.6의 Apache Hadoop 구성 요소 및 버전에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: ee4ae5e0b92fb0627143f2d2f998747f7e956b6d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745147"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3.6 구성 요소 버전

이 문서에서는 Azure HDInsight 3.6의 Apache Hadoop 환경 구성 요소 및 버전에 대해 알아봅니다.

## <a name="support-for-hdinsight-36"></a>HDInsight 3.6에 대 한 지원

다음 표에서는 HDInsight 3.6 클러스터 형식에 대 한 지원 기간을 나열 합니다.

| 클러스터 유형                    | 프레임 워크 버전 | 현재 지원 만료        | 새 지원 만료 날짜 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Spark             | 2.3               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Spark             | 2.2               | 2020 년 6 월 30 일에 사용 중지          |                             |
| HDInsight 3.6 Spark             | 2.1               | 2020 년 6 월 30 일에 사용 중지          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Kafka             | 1.0               | 2020 년 6 월 30 일에 사용 중지 됩니다.         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 대화형 쿼리 | 2.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 스톰             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 ML 서비스      | 9.3               | 2020 년 12 월 31 일                      | 2020 년 12 월 31 일                |
## <a name="apache-components-available-with-hdinsight-version-36"></a>HDInsight 버전 3.6에서 사용할 수 있는 Apache 구성 요소

다음 표에는 HDInsight 3.6와 연결 된 OSS 구성 요소 버전이 나와 있습니다.

| 구성 요소              | HDInsight 3.6(기본값)     |
|------------------------|-----------------------------|
| Apache Hadoop 및 YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 on ESP 대화형 쿼리) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0.4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)