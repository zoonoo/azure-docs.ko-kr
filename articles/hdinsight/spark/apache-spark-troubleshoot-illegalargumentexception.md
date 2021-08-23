---
title: Apache Spark에 대한 IllegalArgumentException 오류 - Azure HDInsight
description: Azure Data Factory용 Azure HDInsight의 Apache Spark 작업에 대한 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 2b892e6419045d362ac8675a6e8976600650851a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288922"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Spark 작업에 대한 IllegalArgumentException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Azure Data Factory 파이프라인에서 Spark 작업을 실행하려고 할 때 다음과 같은 예외가 발생합니다.

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>원인

Spark 클러스터의 기본/주 스토리지에 애플리케이션 jar 파일이 없으면 Spark 작업이 실패합니다.

이것은 이 버그에서 추적된 Spark 오픈 소스 프레임워크와 관련된 알려진 문제입니다. [fs.defaultFS와 애플리케이션 jar의 URL이 다르면 Spark 작업이 실패합니다.](https://issues.apache.org/jira/browse/SPARK-22587)

이 문제는 Spark 2.3.0에서 해결되었습니다.

## <a name="resolution"></a>해결 방법

애플리케이션 jar이 HDInsight 클러스터의 기본/주 스토리지에 저장되어 있는지 확인합니다. Azure Data Factory의 경우 ADF 연결된 서비스가 보조 컨테이너가 아닌 HDInsight 기본 컨테이너를 가리키는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]