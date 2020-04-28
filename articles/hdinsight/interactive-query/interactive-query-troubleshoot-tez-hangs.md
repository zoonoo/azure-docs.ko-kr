---
title: Azure HDInsight에서 응용 프로그램 중지 Apache Tez
description: Azure HDInsight에서 응용 프로그램 중지 Apache Tez
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895108"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 응용 프로그램이 중단 Apache Tez

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive 작업을 제출한 후 Tez 보기에서 작업 상태는 "실행 중" 이지만 진행 중인 것으로 표시 되지 않습니다.

## <a name="cause"></a>원인

너무 많은 작업을 제출 했습니다. 긴 Yarn 큐입니다.

## <a name="resolution"></a>해결 방법

클러스터를 확장 하거나 Yarn 큐가 방전 될 때까지 기다립니다.

는 기본적 `yarn.scheduler.capacity.maximum-applications` 으로 실행 중이거나 보류 중인 응용 프로그램의 최대 수를 제어 하며 기본적으로로 `10000`설정 됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
