---
title: Apache Spark에 대 한 IllegalArgumentException 오류-Azure HDInsight
description: Azure Data Factory에 대 한 Azure HDInsight의 Apache Spark 작업에 대 한 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287915"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Spark 작업에 대 한 IllegalArgumentException

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Azure Data Factory 파이프라인에서 Spark 작업을 실행 하려고 할 때 다음과 같은 예외가 표시 됩니다.

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>원인

Spark 클러스터의 기본/기본 저장소에 응용 프로그램 jar 파일이 없으면 Spark 작업이 실패 합니다.

이 버그는이 버그에서 추적 된 Spark 오픈 소스 프레임 워크의 알려진 문제입니다.. [defaultFS 및 응용 프로그램 jar가 다른 url 인 경우 spark 작업이 실패](https://issues.apache.org/jira/browse/SPARK-22587)합니다.

이 문제는 Spark 2.3.0에서 해결 되었습니다.

## <a name="resolution"></a>해결 방법

응용 프로그램 jar이 HDInsight 클러스터의 기본/기본 저장소에 저장 되어 있는지 확인 합니다. Azure Data Factory 경우 ADF 연결 된 서비스가 보조 컨테이너가 아닌 HDInsight 기본 컨테이너를 가리키는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]