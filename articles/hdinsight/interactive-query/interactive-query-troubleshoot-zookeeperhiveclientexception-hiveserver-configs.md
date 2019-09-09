---
title: Apache Hive Zeppelin 인터프리터는 Azure HDInsight에서 사육 사 오류를 제공 합니다.
description: Apache Zeppelin Hive JDBC 인터프리터는 Azure HDInsight에서 잘못 된 URL을 가리킵니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 308e1cc44fc1c87003a9cb64922f0d933bbfe92d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811411"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>시나리오: Apache Hive Zeppelin 인터프리터는 Azure HDInsight에서 사육 사 오류를 제공 합니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Apache Hive LLAP 클러스터에서 쿼리 실행을 시도할 때 Zeppelin 인터프리터는 다음과 같은 오류 메시지를 제공 합니다.

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>원인

Zeppelin Hive JDBC 인터프리터가 잘못 된 URL을 가리킵니다.

## <a name="resolution"></a>해결 방법

1. Hive 구성 요소 요약으로 이동 하 여 "Hive JDBC Url"을 클립보드에 복사 합니다.

1. 탐색`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. JDBC 설정 편집: 1 단계에서 복사한 Hive JDBC URL의 hive url 값을 업데이트 합니다.

1. 저장 한 다음 쿼리를 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
