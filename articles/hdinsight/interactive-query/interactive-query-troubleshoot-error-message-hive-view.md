---
title: 아파치 하이브 보기에 표시되지 않는 오류 메시지 - Azure HDInsight
description: Azure HDInsight 클러스터에 대한 세부 정보 없이 아파치 하이브 보기에서 쿼리가 실패합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895210"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 아파치 하이브 보기에 표시되지 않는 쿼리 오류 메시지

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 하이브 뷰 쿼리 오류 메시지는 추가 정보 없이 다음과 같이 표시됩니다.

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>원인

경우에 따라 쿼리 오류 메시지가 너무 커서 Hive View 메인 페이지에 표시할 수 없습니다.

## <a name="resolution"></a>해결 방법

Hive_view 오른쪽 상단 모서리에 있는 알림 탭을 확인하여 전체 스택추적 및 오류 메시지를 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
