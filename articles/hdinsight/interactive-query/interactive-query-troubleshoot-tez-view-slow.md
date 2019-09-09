---
title: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드 됩니다.
description: Apache Ambari Tez 뷰가 느리게 로드 되거나 Azure HDInsight에서 전혀 로드 되지 않을 수 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 4a0f6706781b0234942c473187474d0ab66e3cd4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811554"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드 됩니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Apache Ambari Tez 뷰가 느리게 로드 되거나 전혀 로드 되지 않을 수 있습니다. Ambari Tez 뷰를 로드 하는 경우 헤드 노드의 프로세스가 응답 하지 않는 것을 볼 수 있습니다.

## <a name="cause"></a>원인

Yarn ATS Api에 액세스 하는 경우 클러스터에 많은 수의 Hive 작업이 실행 될 때 10 월 2017 일 이전에 생성 된 클러스터의 성능이 저하 될 수 있습니다.

## <a name="resolution"></a>해결 방법

이 문제는 Oct 2017에서 해결 된 문제입니다. 클러스터를 다시 만들면이 문제가 다시 실행 되지 않습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
