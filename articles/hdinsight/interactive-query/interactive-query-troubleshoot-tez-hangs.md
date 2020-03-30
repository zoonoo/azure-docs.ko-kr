---
title: 아파치 테즈 응용 프로그램은 Azure HDInsight에 중단
description: 아파치 테즈 응용 프로그램은 Azure HDInsight에 중단
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895108"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>시나리오: 아파치 테즈 응용 프로그램 Azure HDInsight에 중단

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 하이브 작업을 제출 한 후, Tez에서 작업 상태가 "실행 중"이지만 진행을하지 않는 것으로 보입니다.

## <a name="cause"></a>원인

제출된 작업이 너무 많습니다. 긴 원사 대기열.

## <a name="resolution"></a>해결 방법

클러스터를 확장하거나 원사 대기열이 소진될 때까지 기다립니다.

기본적으로 `yarn.scheduler.capacity.maximum-applications` 실행 중이거나 보류 중인 최대 응용 프로그램 수를 제어하며 기본값으로 `10000`설정합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
