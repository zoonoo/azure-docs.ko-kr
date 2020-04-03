---
title: Azure HDInsight의 아파치 하이브에 대한 게이트웨이 심층 분석 및 모범 사례
description: Azure HDInsight 게이트웨이를 통해 Hive 쿼리를 실행하는 모범 사례를 탐색하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586978"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight의 아파치 하이브에 대한 게이트웨이 심층 분석 및 모범 사례

Azure HDInsight 게이트웨이(게이트웨이)는 HDInsight 클러스터의 HTTPS 프런트엔드입니다. 게이트웨이는 인증, 호스트 해결, 서비스 검색 및 최신 분산 시스템에 필요한 기타 유용한 기능을 담당합니다. 게이트웨이에서 제공하는 기능은 이 문서에서 탐색할 모범 사례를 설명하는 일부 오버헤드를 초래합니다. 게이트웨이 문제 해결 기술도 설명합니다.

## <a name="the-hdinsight-gateway"></a>HDInsight 게이트웨이

HDInsight 게이트웨이는 인터넷을 통해 공개적으로 액세스할 수 있는 HDInsight 클러스터의 유일한 부분입니다. 게이트웨이 서비스는 `clustername-int.azurehdinsight.net` 내부 게이트웨이 끝점을 사용하여 공용 인터넷을 거치지 않고도 액세스할 수 있습니다. 내부 게이트웨이 끝점을 사용하면 클러스터의 가상 네트워크를 종료하지 않고도 게이트웨이 서비스에 대한 연결을 설정할 수 있습니다. 게이트웨이는 클러스터로 전송되는 모든 요청을 처리하고 이러한 요청을 올바른 구성 요소 및 클러스터 호스트로 전달합니다.

다음 다이어그램은 게이트웨이가 HDInsight 내의 모든 다른 호스트 해상도 가능성 앞에서 추상화를 제공하는 방법에 대한 대략적인 그림을 제공합니다.

![호스트 해결 다이어그램](./media/gateway-best-practices/host-resolution-diagram.png "호스트 해결 다이어그램")

## <a name="motivation"></a>동기

HDInsight 클러스터 앞에 게이트웨이를 배치하려는 동기는 서비스 검색 및 사용자 인증을 위한 인터페이스를 제공하는 것입니다. 게이트웨이에서 제공하는 인증 메커니즘은 특히 ESP 지원 클러스터와 관련이 있습니다.

서비스 검색을 위해 게이트웨이의 장점은 클러스터 내의 각 구성 요소가 다양한 `clustername.azurehdinsight.net/hive2` `host:port` 페어링과 달리 게이트웨이 웹 사이트(에서 다른 끝점으로 액세스할 수 있음)입니다.

인증을 위해 게이트웨이를 사용하면 `username:password` 사용자가 자격 증명 쌍을 사용하여 인증할 수 있습니다. ESP 지원 클러스터의 경우 이 자격 증명은 사용자의 도메인 사용자 이름과 암호입니다. 게이트웨이를 통해 HDInsight 클러스터에 대한 인증은 클라이언트가 kerberos 티켓을 획득할 필요가 없습니다. 게이트웨이는 자격 `username:password` 증명을 수락하고 사용자를 대신하여 사용자의 Kerberos 티켓을 획득하므로 ESP(ESP) 클러스터가 아닌 다른 AA-DDS 도메인에 가입한 클라이언트를 포함하여 모든 클라이언트 호스트에서 게이트웨이에 대한 보안 연결을 만들 수 있습니다.

## <a name="best-practices"></a>모범 사례

게이트웨이는 요청 전달 및 인증을 담당하는 단일 서비스(두 호스트간에 부하 분산)입니다. 게이트웨이는 특정 크기를 초과하는 Hive 쿼리의 처리량 병목 현상이 될 수 있습니다. ODBC 또는 JDBC를 통해 게이트웨이에서 매우 큰 **SELECT** 쿼리가 실행될 때 쿼리 성능 저하가 관찰될 수 있습니다. "매우 큰"은 행 또는 열에서 5GB 이상의 데이터를 구성하는 쿼리를 의미합니다. 이 쿼리에는 긴 행 목록 및 광범위한 열 수가 포함될 수 있습니다.

큰 크기의 쿼리에 대한 게이트웨이의 성능 저하는 데이터가 기본 데이터 저장소(ADLS Gen2)에서 HDInsight Hive 서버, 게이트웨이, 마지막으로 JDBC 또는 ODBC 드라이버를 통해 클라이언트 호스트로 전송되어야 하기 때문입니다.

다음 다이어그램은 SELECT 쿼리와 관련된 단계를 보여 줍니다.

![결과 다이어그램](./media/gateway-best-practices/result-retrieval-diagram.png "결과 다이어그램")

아파치 하이브는 HDFS 호환 파일 시스템 위에 관계형 추상화입니다. 이 추상화는 Hive의 **SELECT** 문이 파일 시스템의 **READ** 작업에 해당한다는 것을 의미합니다. **READ** 작업은 사용자에게 보고되기 전에 적절한 스키마로 변환됩니다. 이 프로세스의 대기 시간은 최종 사용자에게 도달하는 데 필요한 데이터 크기와 총 홉에 따라 증가합니다.

이러한 명령은 기본 파일 시스템의 **WRITE** 작업에 해당하므로 큰 데이터의 **CREATE** 또는 **INSERT** 문을 실행할 때도 비슷한 동작이 발생할 수 있습니다. **INSERT** 또는 **LOAD를**사용하여 로드하는 대신 원시 ORC와 같은 데이터를 파일 시스템/데이터 레이크에 쓰는 것이 좋습니다.

엔터프라이즈 보안 팩 지원 클러스터에서 충분히 복잡한 아파치 레인저 정책은 쿼리 컴파일 시간이 느려질 수 있으며 게이트웨이 시간 설정으로 이어질 수 있습니다. ESP 클러스터에서 게이트웨이 시간 지정이 발견되면 레인저 정책 수를 줄이거나 결합하는 것이 좋습니다.

## <a name="troubleshooting-techniques"></a>문제 해결 기술

위의 동작의 일부로 충족된 성능 문제를 완화하고 이해하기 위한 여러 장소가 있습니다. HDInsight 게이트웨이를 통해 쿼리 성능이 저하될 때 다음 검사 목록을 사용합니다.

* 큰 **SELECT** 쿼리를 실행할 때 **LIMIT** 절을 사용합니다. **LIMIT** 절은 클라이언트 호스트에 보고된 총 행을 줄입니다. **LIMIT** 절은 결과 생성에만 영향을 미치며 쿼리 계획을 변경하지 않습니다. 쿼리 계획에 **LIMIT** 절을 적용하려면 구성을 `hive.limit.optimize.enable`사용합니다. **LIMIT는** 인수 양식 **LIMIT x,y를**사용하여 오프셋과 결합할 수 있습니다.

* SELECT 를 사용하는 대신 **SELECT** 쿼리를 실행할 때 관심 있는 열의 이름을 **지정합니다. \* ** 열을 적게 선택하면 읽는 데이터 양이 줄어듭니다.

* 아파치 Beeline을 통해 관심의 쿼리를 실행해보십시오. 아파치 Beeline을 통해 결과 검색 시간이 오랜 시간이 걸리는 경우, 외부 도구를 통해 동일한 결과를 검색 할 때 지연을 기대합니다.

* 기본 Hive 쿼리를 테스트하여 HDInsight 게이트웨이에 대한 연결을 설정할 수 있는지 확인합니다. 두 개 이상의 외부 도구에서 기본 쿼리를 실행하여 개별 도구가 문제가 없는지 확인합니다.

* 아파치 암바리 경고를 검토하여 HDInsight 서비스가 건전한지 확인합니다. Ambari 경고보고 및 분석을 위해 Azure 모니터와 통합될 수 있습니다.

* 외부 Hive 메타스토어를 사용하는 경우 Hive 메타스토어의 Azure SQL DB DTU가 한계에 도달하지 않았는지 확인합니다. DTU가 한계에 가까워지면 데이터베이스 크기를 늘려야 합니다.

* PBI 또는 Tableau와 같은 타사 도구가 페이지 매김을 사용하여 테이블이나 데이터베이스를 볼 수 있는지 확인합니다. 페이지 마기에 대한 지원 도구는 지원 파트너에게 문의하십시오. 페이지 조정에 사용되는 주요 도구는 JDBC `fetchSize` 매개 변수입니다. 가져오기 크기가 작으면 게이트웨이 성능이 저하될 수 있지만 가져오기 크기가 너무 크면 게이트웨이 시간 초과가 발생할 수 있습니다. 가져오기 크기 조정은 워크로드 단위로 수행해야 합니다.

* 데이터 파이프라인에서 HDInsight 클러스터의 기본 저장소에서 많은 양의 데이터를 읽는 경우 Azure 데이터 팩터리와 같은 Azure 저장소와 직접 인터페이스하는 도구를 사용하는 것이 좋습니다.

* LLAP는 쿼리 결과를 빠르게 반환할 수 있는 더 원활한 환경을 제공할 수 있기 때문에 대화형 워크로드를 실행할 때 아파치 Hive LLAP를 사용하는 것이 좋습니다.

* 을 사용하여 `hive.server2.thrift.max.worker.threads`Hive Metastore 서비스에 사용할 수 있는 스레드 수를 늘리는 것이 좋습니다. 이 설정은 많은 수의 동시 사용자가 클러스터에 쿼리를 제출할 때 특히 관련이 있습니다.

* 외부 도구에서 게이트웨이에 도달하는 데 사용되는 재시도 횟수를 줄입니다. 여러 번의 재시도를 사용하는 경우 재시도 정책을 지수로 되돌리는 것이 좋습니다.

* 구성 `hive.exec.compress.output` 및 `hive.exec.compress.intermediate`.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 아파치 비라인](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [HDInsight 게이트웨이 시간 시간 문제 해결 단계](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [HDInsight를 위한 가상 네트워크](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [고속 경로가 있는 HDInsight](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)