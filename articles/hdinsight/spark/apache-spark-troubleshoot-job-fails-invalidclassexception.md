---
title: 아파치 스파크에서 잘못된 클래스예외 오류 - Azure HDInsight
description: 아파치 스파크 작업 InvalidClass예외, 클래스 버전 불일치, Azure HDInsight에서 실패
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894359"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>아파치 스파크 작업 InvalidClass예외, 클래스 버전 불일치, Azure HDInsight에서 실패

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

스파크 2.x 클러스터에서 아파치 스파크 작업을 만들려고 합니다. 다음과 유사한 오류로 실패합니다.

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

이 오류는 `spark.yarn.jars` 구성에 추가 jar, 특히 `commons-lang3` 다른 버전의 패키지를 포함 하 고 클래스 불일치를 소개 하는 그늘진된 jar를 추가 하 여 발생할 수 있습니다. 기본적으로 스파크 `commons-lang3`2.1/2/3은 의 버전 3.5를 사용합니다.

> [!TIP]
> 라이브러리를 음영처리하려면 내용물을 자신의 항아리에 넣고 패키지를 변경하는 것입니다. 이는 라이브러리를 다시 패키징하지 않고 자신의 항아리에 넣는 라이브러리를 패키징하는 것과 다릅니다.

## <a name="resolution"></a>해결 방법

jar을 제거하거나 사용자 지정된 jar(AzureLogAppender)을 다시 컴파일하고 [maven-shade-플러그인을](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) 사용하여 클래스를 재배치합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
