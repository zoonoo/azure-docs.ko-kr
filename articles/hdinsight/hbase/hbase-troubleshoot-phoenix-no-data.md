---
title: HDP 업그레이드를 수행 하면 Azure HDInsight의 Apache Phoenix 보기에서 데이터가 나타나지 않음
description: HDP 업그레이드를 수행 하면 Azure HDInsight의 Apache Phoenix 보기에서 데이터가 나타나지 않음
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 94423fa1f7596eb42ad6112003ad202b2eb21766
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091625"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>시나리오: HDP 업그레이드를 수행 하면 Azure HDInsight의 Apache Phoenix 보기에서 데이터가 나타나지 않음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

HDP 2.4에서 HDP 2.5로 업그레이드 한 후 Apache Phoenix 보기에 날짜가 포함 되지 않습니다.

## <a name="cause"></a>원인

뷰의 인덱스 테이블 (뷰의 모든 인덱스는 단일 실제 Apache HBase 테이블에 저장 됨)은 업그레이드 하는 동안 잘립니다.

## <a name="resolution"></a>해결 방법

업그레이드 후 모든 뷰 인덱스를 삭제 하 고 다시 만듭니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
