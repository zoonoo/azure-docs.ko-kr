---
title: Apache Spark에서 InvalidClassException 오류 발생-Azure HDInsight
description: Azure HDInsight에서 InvalidClassException, 클래스 버전이 일치 하지 않으므로 Apache Spark 작업이 실패 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 6220c328d05e7cd68460b7bfd0708a9d393a290f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287893"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]