---
title: 아파치 암바리 테즈 보기 Azure HDInsight에서 천천히 로드
description: 아파치 암바리 테즈 보기 느리게 로드 될 수 있습니다 또는 Azure HDInsight에서 전혀 로드 되지 않을 수 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895102"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>시나리오: 아파치 암바리 테즈 보기 Azure HDInsight에서 천천히 로드

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 암바리 테즈 뷰는 느리게 로드되거나 전혀 로드되지 않을 수 있습니다. Ambari Tez View를 로드할 때 헤드노드의 프로세스가 응답하지 않는 것을 볼 수 있습니다.

## <a name="cause"></a>원인

Yarn ATS API에 액세스하면 클러스터에 많은 수의 Hive 작업이 실행되는 2017년 10월 이전에 생성된 클러스터의 성능이 저하될 수 있습니다.

## <a name="resolution"></a>해결 방법

이것은 2017년 10월에 수정된 문제입니다. 클러스터를 다시 만들면 이 문제가 다시 실행되지 않습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
