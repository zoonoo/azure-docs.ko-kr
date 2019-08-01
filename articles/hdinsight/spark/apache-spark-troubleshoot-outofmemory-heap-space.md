---
title: Azure HDInsight에서 Apache Spark 기록 서버를 열려고 할 때 Java 힙 공간 오류가 발생 합니다.
description: Apache Livy 서버를 Azure HDInsight의 OutOfMemoryError로 시작 하지 못함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667795"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Spark 기록 서버를 열려고 할 때 Java 힙 공간 오류가 발생 합니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Spark 기록 서버에서 이벤트를 열 때 다음과 같은 오류가 표시 됩니다.

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>원인

이 문제는 많은 spark 이벤트 파일을 열 때 리소스가 부족 하 여 발생 하는 경우가 많습니다. Spark 힙 크기는 기본적으로 1gb로 설정 되지만 큰 Spark 이벤트 파일에는이 보다 많은 작업이 필요할 수 있습니다.

로드 하려는 파일의 크기를 확인 하려는 경우 다음 명령을 수행할 수 있습니다.

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>해결 방법

Spark 구성에서 속성을 `SPARK_DAEMON_MEMORY` 편집 하 고 모든 서비스를 다시 시작 하 여 spark 기록 서버 메모리를 늘릴 수 있습니다.

Spark2/Config/Advanced Spark2 섹션을 선택 하 여 Ambari 브라우저 UI 내에서이 작업을 수행할 수 있습니다.

![Advanced spark2-env 섹션](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

다음 속성을 추가 하 여 Spark 기록 서버 메모리를 1g에서 4g:로 `SPARK_DAEMON_MEMORY=4g`변경 합니다.

![Spark 속성](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Ambari에서 영향을 받는 모든 서비스를 다시 시작 해야 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
