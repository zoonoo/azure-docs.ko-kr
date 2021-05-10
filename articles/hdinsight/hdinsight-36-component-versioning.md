---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight 3.6
description: Azure HDInsight 3.6의 Apache Hadoop 구성 요소 및 버전에 대해 자세히 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727691"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3.6 구성 요소 버전

이 문서에서는 Azure HDInsight 3.6의 Apache Hadoop 환경 구성 요소 및 버전에 대해 알아봅니다.

## <a name="support-for-hdinsight-36"></a>HDInsight 3.6 지원

2021년 7월 1일부터 Microsoft는 특정 HDI 3.6 클러스터 형식에 대한 기본 지원을 제공합니다.
다음 표에는 HDInsight 3.6 클러스터 유형에 대한 지원 기간이 나와 있습니다.

| 클러스터 유형                    | 프레임워크 버전 | 표준 지원 만료       | 기본 지원 만료 날짜 | 사용 중지 날짜 |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 Spark             | 2.3               | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 Kafka             | 1.1               | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 HBase             | 1.1               | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 Interactive Query | 2.1               | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 Storm             | 1.1               | 2021년 6월 30일                     | 2022년 4월 3일                | 2022년 4월 4일 |
| HDInsight 3.6 ML Services      | 9.3               | -                                 | -                            | 2020년 12월 31일 |
| HDInsight 3.6 Spark             | 2.2               | -                                 | -                            | 2020년 6월 30일 |
| HDInsight 3.6 Spark             | 2.1               | -                                 | -                            | 2020년 6월 30일 |
| HDInsight 3.6 Kafka             | 1.0               | -                                 | -                            | 2020년 6월 30일 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>HDInsight 버전 3.6에서 제공되는 Apache 구성 요소

HDInsight 3.6과 연결된 OSS 구성 요소 버전은 다음 표에 나열되어 있습니다.

| 구성 요소              | HDInsight 3.6(기본값)     |
|------------------------|-----------------------------|
| Apache Hadoop 및 YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (ESP Interactive Query의 2.1.0) |
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
