---
title: Azure HDInsight의 Apache Hive에 대한 게이트웨이 심층 분석 및 모범 사례
description: Azure HDInsight 게이트웨이를 통해 Hive 쿼리를 실행하기 위한 모범 사례를 탐색하는 방법을 알아봅니다
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 549eab1547b75eb9461b23df2c157290943b4ed9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869789"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight의 Apache Hive에 대한 게이트웨이 심층 분석 및 모범 사례

Azure HDInsight gateway(게이트웨이)는 HDInsight 클러스터에 대한 HTTPS 프런트 엔드입니다. 게이트웨이는 인증, 호스트 확인, 서비스 검색 및 최신 분산 시스템에 필요한 기타 유용한 기능을 담당합니다. 게이트웨이에서 제공하는 기능에 따라 이 문서에서 탐색할 모범 사례를 설명하는 오버 헤드가 발생합니다. 게이트웨이 문제 해결 기술에 대해서도 설명합니다.

## <a name="the-hdinsight-gateway"></a>HDInsight 게이트웨이

HDInsight 게이트웨이는 인터넷을 통해 공개적으로 액세스할 수 있는 HDInsight 클러스터의 유일한 부분입니다. `clustername-int.azurehdinsight.net`내부 게이트웨이 엔드포인트를 사용하여 공용 인터넷을 통하지 않고 게이트웨이 서비스에 액세스할 수 있습니다. 내부 게이트웨이 엔드포인트를 사용하면 클러스터의 가상 네트워크를 종료하지 않고 게이트웨이 서비스에 대한 연결을 설정할 수 있습니다. 게이트웨이는 클러스터에 전송된 모든 요청을 처리하고 이러한 요청을 올바른 구성 요소 및 클러스터 호스트에 전달합니다.

다음 다이어그램에서는 게이트웨이가 HDInsight 내의 다른 모든 호스트 확인 가능성 앞에 추상화를 제공하는 방법에 대한 대략적인 설명을 제공합니다.

:::image type="content" source="./media/gateway-best-practices/host-resolution-diagram.png " alt-text="호스트 확인 다이어그램" border="true":::

## <a name="motivation"></a>동기

게이트웨이를 HDInsight 클러스터 앞에 배치하는 것은 서비스 검색 및 사용자 인증을 위한 인터페이스를 제공하는 것입니다. 게이트웨이에서 제공하는 인증 메커니즘은 특히 ESP 지원 클러스터와 관련이 있습니다.

서비스 검색의 경우 게이트웨이의 장점은 많은 `host:port` 쌍과는 달리 클러스터 내의 각 구성 요소를 게이트웨이 웹 사이트 (`clustername.azurehdinsight.net/hive2`)에서 다른 엔드포인트로 액세스할 수 있다는 점입니다.

인증을 위해 게이트웨이를 통해 사용자는 `username:password`자격 증명 쌍을 사용하여 인증할 수 있습니다. ESP 사용 클러스터의 경우 이 자격 증명은 사용자의 도메인 사용자 이름 및 암호입니다. 게이트웨이를 통한 HDInsight 클러스터 인증에는 클라이언트가 kerberos 티켓을 가져올 필요가 없습니다. 게이트웨이는 `username:password` 자격 증명을 수락하고 사용자를 대신하여 사용자의 Kerberos 티켓을 획득하므로 (ESP) 클러스터와 다른 AA DDS 도메인에 가입된 클라이언트를 포함하여 모든 클라이언트 호스트에서 게이트웨이에 대한 보안 연결을 설정할 수 있습니다.

## <a name="best-practices"></a>모범 사례

게이트웨이는 요청 전달 및 인증을 담당하는 단일 서비스(두 호스트에 분산된 부하)입니다. 게이트웨이가 특정 크기를 초과하는 Hive 쿼리에 대한 처리량 병목 상태가 될 수 있습니다. ODBC 또는 JDBC를 통해 게이트웨이에서 매우 큰 **SELECT** 쿼리가 실행되는 경우 쿼리 성능 저하가 관찰될 수 있습니다. "매우 큼"은 행 또는 열에서 5GB 이상의 데이터를 구성하는 쿼리를 의미합니다. 이 쿼리에는 긴 행 목록과 또는 광범위한 열 수가 포함될 수 있습니다.

게이트웨이의 성능 저하는 데이터를 기본 데이터 저장소(ADLS Gen2)에서 HDInsight Hive 서버, 게이트웨이, 그리고 마지막으로 JDBC 또는 ODBC 드라이버를 통해 클라이언트 호스트로 전송해야 하기 때문에 발생합니다.

다음 다이어그램은 SELECT 쿼리와 관련된 단계를 보여줍니다.

:::image type="content" source="./media/gateway-best-practices/result-retrieval-diagram.png " alt-text="결과 다이어그램" border="true":::

Apache Hive는 HDFS 호환 파일 시스템에 대한 관계형 추상화입니다. 이 추상화는 Hive의 **SELECT** 문이 파일 시스템에 대한 **읽기** 작업과 일치함을 의미합니다. **읽기** 작업은 사용자에게 보고되기 전에 적절한 스키마로 변환됩니다. 최종 사용자에게 도달하는 데 필요한 데이터 크기 및 총 홉으로 이 프로세스의 대기 시간이 증가합니다.

이러한 명령이 기본 파일 시스템의 **WRITE** 작업에 해당하므로 많은 데이터의 **CREATE** 또는 **INSERT** 문을 실행할 때 비슷한 동작이 발생할 수 있습니다. **INSERT** 또는 **LOAD** 를 사용하여 로드하는 대신 원시 ORC 같은 데이터를 filesystem/datalake에 기록하는 것이 좋습니다.

엔터프라이즈 보안 팩 사용 클러스터에서 매우 복잡한 Apache Ranger 정책으로 인해 쿼리 컴파일 시간이 느려질 수 있으며, 이로 인해 게이트웨이 시간 초과가 발생할 수 있습니다. ESP 클러스터에서 게이트웨이 시간 제한이 발견되면 Ranger 정책 수를 줄이거나 결합하는 것이 좋습니다.

## <a name="troubleshooting-techniques"></a>문제 해결 기술

위 동작의 일부로 인해 성능 문제를 완화하고 이해하는 데는 여러 장소가 있습니다. HDInsight 게이트웨이를 통한 쿼리 성능 저하가 발생하는 경우 다음 검사 목록을 사용합니다.

* 대규모 **SELECT** 쿼리를 실행할 때 **LIMIT** 절을 사용합니다. **LIMIT** 절은 클라이언트 호스트에 보고된 총 행 수를 줄입니다. **LIMIT** 절은 결과 생성에만 영향을 미치고 쿼리 계획을 변경하지는 않습니다. **LIMIT** 절을 쿼리 계획에 적용하려면 구성`hive.limit.optimize.enable`을 사용합니다. **LIMIT** **LIMIT x, y** 인수 형태를 사용하여 오프셋과 결합할 수 있습니다.

* **Select\*** 사용하는 대신 **select** 쿼리를 실행할 때 원하는 열의 이름을 지정합니다. 적은 수의 열을 선택하면 읽을 데이터의 양이 줄어듭니다.

* Apache Beeline를 통해 관심있는 쿼리를 실행해 보세요. Apache Beeline을 통한 결과 검색에 오랜 시간이 소요되는 경우 외부 도구를 통해 동일한 결과를 검색할 때 지연이 발생합니다.

* 기본 Hive 쿼리를 테스트하여 HDInsight 게이트웨이에 대한 연결을 설정할 수 있는지 확인합니다. 두 개 이상의 외부 도구에서 기본 쿼리를 실행하여 문제가 발생하는 개별 도구가 없는지 확인하세요.

* Apache Ambari 경고를 검토하여 HDInsight 서비스가 정상 상태인지 확인합니다. Ambari 경고는 보고 및 분석을 위해 Azure Monitor와 통합할 수 있습니다.

* 외부 Hive Metastore를 사용하는 경우 Hive Metastore에 대한 Azure SQL DB DTU가 제한에 도달하지 않았는지 확인합니다. DTU의 제한에 도달하는 경우 데이터베이스 크기를 늘려야 합니다.

* PBI 또는 Tableau와 같은 타사 도구에서 페이지 매김을 사용하여 테이블 또는 데이터베이스를 표시하는지 확인합니다. 페이지 매김에 대한 지원은 이러한 도구에 대한 지원 파트너에게 문의하세요. 페이지 매김에 사용되는 기본 도구는 JDBC `fetchSize` 매개 변수입니다. 반입 크기가 작으면 게이트웨이 성능이 저하될 수 있지만 반입 크기가 너무 커지면 게이트웨이 시간이 초과될 수 있습니다. 반입 크기 조정은 작업 단위로 수행해야 합니다.

* 데이터 파이프라인이 HDInsight 클러스터의 기본 스토리지에서 다량의 데이터를 읽는 경우 Azure Data Factory와 같은 Azure Storage와 직접 상호 작용하는 도구 사용을 고려하는 것이 좋습니다

* LLAP이 쿼리 결과를 신속하게 반환할 수 있는 더 부드러운 환경을 제공할 수 있으므로 대화형 작업을 실행할 때 LLAP Apache Hive 사용하는 것이 좋습니다

* `hive.server2.thrift.max.worker.threads`을 사용하여 Hive Metastore 서비스에 사용할 수 있는 스레드 수를 늘립니다. 이 설정은 매우 많은 수의 동시 사용자가 클러스터에 쿼리를 제출하는 경우에 특히 관련이 있습니다

* 외부 도구에서 게이트웨이에 연결하는 데 사용되는 재시도 횟수를 줄입니다. 여러 번 다시 시도하는 경우에는 지수 백오프 재시도 정책에 따라 다음을 고려하십시오

* `hive.exec.compress.output` 및 `hive.exec.compress.intermediate` 구성을 사용하여 압축 Hive를 사용하도록 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Beeline](../hadoop/apache-hadoop-use-hive-beeline.md)
* [HDInsight 게이트웨이 시간 제한 문제 해결 단계](./troubleshoot-gateway-timeout.md)
* [HDInsight 용 가상 네트워크](../hdinsight-plan-virtual-network-deployment.md)
* [기본 경로를 사용하는 HDInsight](../connect-on-premises-network.md)