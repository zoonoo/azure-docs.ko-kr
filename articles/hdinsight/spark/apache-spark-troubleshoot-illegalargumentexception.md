---
title: 아파치 스파크에 대한 불법 인수예외 오류 - Azure HDInsight
description: Azure 데이터 팩터리용 Azure HDInsight에서 아파치 스파크 활동에 대한 불법 인수예외
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894374"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 아파치 스파크 활동에 대 한 불법 인수 예외

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

Azure 데이터 팩터리 파이프라인에서 Spark 활동을 실행하려고 할 때 다음과 같은 예외가 발생합니다.

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>원인

응용 프로그램 jar 파일이 Spark 클러스터의 기본/기본 저장소에 없는 경우 Spark 작업이 실패합니다.

이 버그에서 추적되는 Spark 오픈 소스 프레임워크의 알려진 [문제입니다.](https://issues.apache.org/jira/browse/SPARK-22587)

이 문제는 Spark 2.3.0에서 해결되었습니다.

## <a name="resolution"></a>해결 방법

응용 프로그램 jar이 HDInsight 클러스터의 기본/기본 저장소에 저장되어 있는지 확인합니다. Azure 데이터 팩터리의 경우 ADF 연결 서비스가 보조 컨테이너가 아닌 HDInsight 기본 컨테이너를 가리키는지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
