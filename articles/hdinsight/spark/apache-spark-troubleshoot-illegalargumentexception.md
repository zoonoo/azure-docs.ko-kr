---
title: Azure HDInsight의 Apache Spark 작업에 대 한 IllegalArgumentException
description: Azure Data Factory에 대 한 Azure HDInsight의 Apache Spark 작업에 대 한 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620845"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Spark 작업에 대 한 IllegalArgumentException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Azure Data Factory 파이프라인에서 Spark 작업을 실행 하려고 할 때 다음과 같은 예외가 표시 됩니다.

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>원인

Spark 클러스터의 기본/기본 저장소에 응용 프로그램 jar 파일이 없으면 Spark 작업이 실패 합니다.

이 버그는이 버그에서 추적 된 Spark 오픈 소스 프레임 워크의 알려진 문제입니다. [Fs. defaultFS 및 응용 프로그램 jar가 다른 url 인 경우 Spark 작업이 실패](https://issues.apache.org/jira/browse/SPARK-22587)합니다.

이 문제는 Spark 2.3.0에서 해결 되었습니다.

## <a name="resolution"></a>해결 방법

응용 프로그램 jar이 HDInsight 클러스터의 기본/기본 저장소에 저장 되어 있는지 확인 합니다. Azure Data Factory 경우 ADF 연결 된 서비스가 보조 컨테이너가 아닌 HDInsight 기본 컨테이너를 가리키는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
