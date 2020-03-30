---
title: 아파치 하이브에 합류 아웃 오브 메모리 오류에 이르게 - Azure HDInsight
description: OutOfMemory 오류 처리 "GC 오버헤드 한계 초과 오류"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895181"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>시나리오: 아파치 하이브에 합류하면 Azure HDInsight에서 OutOfMemory 오류가 발생합니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 하이브 조인의 기본 동작은 맵/축소 단계를 수행하지 않고도 조인을 수행할 수 있도록 테이블의 전체 내용을 메모리에 로드하는 것입니다. Hive 테이블이 너무 커서 메모리에 맞지 않으면 쿼리가 실패할 수 있습니다.

## <a name="cause"></a>원인

충분한 크기의 하이브에서 조인을 실행하는 경우 다음과 같은 오류가 발생합니다.

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>해결 방법

다음 Hive 구성 값을 설정하여 하이브가 조인에서 테이블을 메모리에 로드하지 못하게 합니다(대신 맵/축소 단계를 수행).

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>다음 단계

이 값을 설정해도 문제가 해결되지 않으면 다음 중 하나를 방문하십시오.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
