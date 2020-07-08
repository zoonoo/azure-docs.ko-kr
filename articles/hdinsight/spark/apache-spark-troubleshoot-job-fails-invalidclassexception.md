---
title: Apache Spark에서 InvalidClassException 오류 발생-Azure HDInsight
description: Azure HDInsight에서 InvalidClassException, 클래스 버전이 일치 하지 않으므로 Apache Spark 작업이 실패 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894359"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Azure HDInsight에서 InvalidClassException, 클래스 버전이 일치 하지 않으므로 Apache Spark 작업이 실패 합니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Spark 2.x 클러스터에서 Apache Spark 작업을 만들려고 시도 합니다. 다음과 유사한 오류가 발생 하 여 실패 합니다.

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>원인

이 오류는 구성에 추가 jar을 추가 하 여 발생할 수 있습니다 `spark.yarn.jars` . 특히, 다른 버전의 패키지를 포함 하는 음영이 있는 jar를 추가 하 `commons-lang3` 고 클래스 불일치를 소개 합니다. 기본적으로 Spark 2.1/2/3은 버전 3.5를 사용 `commons-lang3` 합니다.

> [!TIP]
> 라이브러리를 음영 처리 하려면 해당 패키지를 변경 하는 자체 jar에 해당 내용을 저장 합니다. 이는 라이브러리를 패키지화 하는 것과 다르며,이는 라이브러리를 다시 패키지 없이 고유한 jar에 배치 하는 것입니다.

## <a name="resolution"></a>해결 방법

Jar를 제거 하거나 사용자 지정 된 jar (AzureLogAppender)을 다시 컴파일하고 [maven](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) -를 사용 하 여 클래스의 위치를 다시 지정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
