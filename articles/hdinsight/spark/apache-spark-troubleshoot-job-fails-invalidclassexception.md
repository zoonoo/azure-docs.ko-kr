---
title: Apache Spark InvalidClassException 오류 - Azure HDInsight
description: Apache Spark는 Azure HDInsight에서 클래스 버전 불일치로 InvalidClassException 오류로 작업이 실패합니다
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929258"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark는 Azure HDInsight에서 클래스 버전 불일치로 InvalidClassException 오류로 작업이 실패합니다

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Spark 2.x 클러스터에서 Apache Spark 작업을 생성하려고 합니다. 다음과 같은 오류가 발생하며 실패합니다.

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

이 오류는 `spark.yarn.jars` 구성에 jar을 더 추가하여 발생할 수 있습니다. 특히 다른 버전의 `commons-lang3` 패키지를 포함하고 클래스 불일치를 도입하는 셰이딩된 jar일 경우에 발생할 수 있습니다. 기본적으로 Spark 2.1/2/3은 `commons-lang3`의 버전 3.5를 사용합니다.

> [!TIP]
> 라이브러리를 셰이딩하려면 콘텐츠를 사용자 고유의 jar에 넣고 패키지를 변경하는 것입니다. 이는 라이브러리를 패키징하는 것과 다르며, 다시 패키징하지 않고 라이브러리를 사용자 고유의 jar에 배치합니다.

## <a name="resolution"></a>해결 방법

jar를 제거하거나 사용자 지정된 jar(AzureLogAppender)를 다시 컴파일하고 [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html)을 사용하여 클래스를 재배치합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]