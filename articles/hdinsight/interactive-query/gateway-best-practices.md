---
title: Azure HDInsight의 Apache Hive에 대 한 게이트웨이 심층 정보 및 모범 사례
description: Azure HDInsight gateway를 통해 Hive 쿼리를 실행 하기 위한 모범 사례를 탐색 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 3db411df69a754857220867865522f8e4fa24030
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546010"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight의 Apache Hive에 대 한 게이트웨이 심층 정보 및 모범 사례

Azure HDInsight gateway (게이트웨이)는 HDInsight 클러스터에 대 한 HTTPS 프런트 엔드입니다. 게이트웨이는 인증, 호스트 확인, 서비스 검색 및 최신 분산 시스템에 필요한 기타 유용한 기능을 담당 합니다. 게이트웨이에서 제공 하는 기능에 따라이 문서에서 탐색할 모범 사례를 설명 하는 오버 헤드가 발생 합니다. 게이트웨이 문제 해결 기술에 대해서도 설명 합니다.

## <a name="the-hdinsight-gateway"></a>HDInsight 게이트웨이

HDInsight 게이트웨이는 인터넷을 통해 공개적으로 액세스할 수 있는 HDInsight 클러스터의 유일한 부분입니다. 내부 게이트웨이 끝점을 사용 하 여 공용 인터넷을 통하지 않고 게이트웨이 서비스에 액세스할 수 있습니다 `clustername-int.azurehdinsight.net` . 내부 게이트웨이 끝점을 사용 하면 클러스터의 가상 네트워크를 종료 하지 않고 게이트웨이 서비스에 대 한 연결을 설정할 수 있습니다. 게이트웨이는 클러스터에 전송 된 모든 요청을 처리 하 고 이러한 요청을 올바른 구성 요소 및 클러스터 호스트에 전달 합니다.

다음 다이어그램에서는 게이트웨이가 HDInsight 내의 다른 모든 호스트 확인 가능성 앞에 추상화를 제공 하는 방법에 대 한 대략적인 설명을 제공 합니다.

![호스트 확인 다이어그램](./media/gateway-best-practices/host-resolution-diagram.png "호스트 확인 다이어그램")

## <a name="motivation"></a>동기

게이트웨이를 HDInsight 클러스터 앞에 배치 하는 것은 서비스 검색 및 사용자 인증을 위한 인터페이스를 제공 하는 것입니다. 게이트웨이에서 제공 하는 인증 메커니즘은 특히 ESP 지원 클러스터와 관련이 있습니다.

서비스 검색의 경우 게이트웨이의 장점은 클러스터 내의 각 구성 요소를 게이트웨이 웹 사이트 ()에서 다른 끝점으로 액세스할 수 있다는 점입니다 `clustername.azurehdinsight.net/hive2` `host:port` .

인증을 위해 게이트웨이를 통해 사용자는 자격 증명 쌍을 사용 하 여 인증할 수 있습니다 `username:password` . ESP 사용 클러스터의 경우이 자격 증명은 사용자의 도메인 사용자 이름 및 암호입니다. 게이트웨이를 통한 HDInsight 클러스터 인증에는 클라이언트에서 kerberos 티켓을 가져올 필요가 없습니다. 게이트웨이는 `username:password` 자격 증명을 수락 하 고 사용자를 대신 하 여 사용자의 Kerberos 티켓을 획득 하므로 (ESP) 클러스터와 다른 AA DDS 도메인에 가입 된 클라이언트를 포함 하 여 모든 클라이언트 호스트에서 게이트웨이에 대 한 보안 연결을 설정할 수 있습니다.

## <a name="best-practices"></a>최선의 구현 방법

게이트웨이는 요청 전달 및 인증을 담당 하는 단일 서비스 (두 호스트에 분산 된 부하 분산)입니다. 게이트웨이가 특정 크기를 초과 하는 Hive 쿼리에 대 한 처리량 병목 상태가 될 수 있습니다. ODBC 또는 JDBC를 통해 게이트웨이에서 매우 큰 **SELECT** 쿼리가 실행 되는 경우 쿼리 성능 저하가 관찰 될 수 있습니다. "매우 큼"은 행 또는 열에서 5gb 이상의 데이터를 구성 하는 쿼리를 의미 합니다. 이 쿼리에는 긴 행 목록과 또는 광범위 한 열 수가 포함 될 수 있습니다.

게이트웨이의 성능 저하는 데이터를 기본 데이터 저장소 (ADLS Gen2)에서 HDInsight Hive 서버, 게이트웨이, 클라이언트 호스트에 대 한 JDBC 또는 ODBC 드라이버를 통해 전송 해야 하기 때문에 발생 합니다.

다음 다이어그램은 SELECT 쿼리와 관련 된 단계를 보여 줍니다.

![결과 다이어그램](./media/gateway-best-practices/result-retrieval-diagram.png "결과 다이어그램")

Apache Hive는 HDFS 호환 파일 시스템에 대 한 관계 추상화입니다. 이 추상화는 Hive의 **SELECT** 문이 파일 시스템에 대 한 **읽기** 작업과 일치 함을 의미 합니다. **읽기** 작업은 사용자에 게 보고 되기 전에 적절 한 스키마로 변환 됩니다. 최종 사용자에 게 도달 하는 데 필요한 데이터 크기 및 총 홉으로이 프로세스의 대기 시간이 증가 합니다.

이러한 명령이 기본 파일 시스템의 **쓰기** 작업에 해당 하므로 많은 데이터의 **CREATE** 또는 **INSERT** 문을 실행할 때 비슷한 동작이 발생할 수 있습니다. **INSERT** 또는 **LOAD** 를 사용 하 여 로드 하는 대신 raw ORC 같은 데이터를 filesystem/datalake에 기록 하는 것이 좋습니다.

엔터프라이즈 보안 팩 사용 클러스터에서 매우 복잡 한 Apache 레인저 정책으로 인해 쿼리 컴파일 시간이 느려질 수 있으며,이로 인해 게이트웨이 시간 초과가 발생할 수 있습니다. ESP 클러스터에서 게이트웨이 시간 제한이 발견 되 면 레인저 정책 수를 줄이거나 결합 하는 것이 좋습니다.

## <a name="troubleshooting-techniques"></a>문제 해결 기술

위 동작의 일부로 인해 성능 문제를 완화 하 고 이해 하는 데는 여러 장소 있습니다. HDInsight 게이트웨이를 통한 쿼리 성능 저하가 발생 하는 경우 다음 검사 목록을 사용 합니다.

* Large **SELECT** 쿼리를 실행할 때 **LIMIT** 절을 사용 합니다. **LIMIT** 절은 클라이언트 호스트에 보고 된 총 행 수를 줄입니다. **LIMIT** 절은 결과 생성에만 영향을 미치고 쿼리 계획을 변경 하지는 않습니다. **LIMIT** 절을 쿼리 계획에 적용 하려면 구성을 사용 `hive.limit.optimize.enable` 합니다. **Limit** **x, y** 인수를 사용 하 여 한도를 오프셋과 결합할 수 있습니다.

* * *Select \** _를 사용 하는 대신 **select** 쿼리를 실행할 때 원하는 열의 이름을로 합니다. 적은 수의 열을 선택 하면 읽을 데이터의 양이 줄어듭니다.

_ Apache Beeline를 통해 관심 있는 쿼리를 실행 해 보세요. Apache Beeline을 통한 결과 검색에 오랜 시간이 소요 되는 경우 외부 도구를 통해 동일한 결과를 검색할 때 지연이 발생 합니다.

* 기본 Hive 쿼리를 테스트 하 여 HDInsight 게이트웨이에 대 한 연결을 설정할 수 있는지 확인 합니다. 두 개 이상의 외부 도구에서 기본 쿼리를 실행 하 여 문제가 발생 하는 개별 도구가 없는지 확인 하세요.

* Apache Ambari 경고를 검토 하 여 HDInsight 서비스가 정상 상태 인지 확인 합니다. Ambari 경고는 보고 및 분석을 위해 Azure Monitor와 통합할 수 있습니다.

* 외부 Hive Metastore를 사용 하는 경우 Hive Metastore에 대 한 Azure SQL DB DTU가 제한에 도달 하지 않았는지 확인 합니다. DTU의 제한에 도달 하는 경우 데이터베이스 크기를 늘려야 합니다.

* PBI 또는 Tableau와 같은 타사 도구에서 페이지 매김을 사용 하 여 테이블 또는 데이터베이스를 표시 하는지 확인 합니다. 페이지 매김에 대 한 지원은 이러한 도구에 대 한 지원 파트너에 게 문의 하세요. 페이지 매김에 사용 되는 기본 도구는 JDBC `fetchSize` 매개 변수입니다. 인출 크기가 작으면 게이트웨이 성능이 저하 될 수 있지만 인출 크기가 너무 커지면 게이트웨이 시간이 초과 될 수 있습니다. Fetch 크기 조정은 작업 단위로 수행 해야 합니다.

* 데이터 파이프라인이 HDInsight 클러스터의 기본 저장소에서 다량의 데이터를 읽는 경우와 같은 Azure Storage와 직접 상호 작용 하는 도구를 사용 하는 것이 좋습니다 Azure Data Factory

* LLAP이 쿼리 결과를 신속 하 게 반환할 수 있는 더 부드러운 환경을 제공할 수 있으므로 대화형 작업을 실행할 때 LLAP Apache Hive 사용 하는 것이 좋습니다.

* 을 사용 하 여 Hive Metastore 서비스에 사용할 수 있는 스레드 수를 늘립니다 `hive.server2.thrift.max.worker.threads` . 이 설정은 매우 많은 수의 동시 사용자가 클러스터에 쿼리를 제출 하는 경우에 특히 관련이 있습니다.

* 외부 도구에서 게이트웨이에 연결 하는 데 사용 되는 재시도 횟수를 줄입니다. 여러 번 다시 시도 하는 경우에는 지 수 백오프 재시도 정책에 따라 다음을 고려 하십시오.

* 및 구성을 사용 하 여 압축 Hive를 사용 하도록 설정 하는 것이 좋습니다 `hive.exec.compress.output` `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Beeline](../hadoop/apache-hadoop-use-hive-beeline.md)
* [HDInsight 게이트웨이 시간 제한 문제 해결 단계](./troubleshoot-gateway-timeout.md)
* [HDInsight 용 가상 네트워크](../hdinsight-plan-virtual-network-deployment.md)
* [Express 경로를 사용 하는 HDInsight](../connect-on-premises-network.md)