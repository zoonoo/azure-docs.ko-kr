---
title: Azure HDInsight 클러스터에 로그인 할 수 없습니다.
description: Azure HDInsight에서 Apache Hadoop 클러스터에 로그인 할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 059fee89fdb7f0bbc046c98e36c6386d06653fff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540417"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터에 로그인 할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Azure HDInsight 클러스터에 로그인 할 수 없습니다.

## <a name="cause"></a>원인

이유는 다를 수 있습니다. 클러스터 또는 앱 대시보드에 로그인 하는 경우 "클러스터 로그인" 또는 HTTP 자격 증명을 사용 합니다. 원격에서 연결하는 경우, SSH(Secure Shell) 또는 원격 데스크톱 자격 증명을 사용합니다.

## <a name="resolution"></a>해결 방법

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

* 비공개 모드의 새 브라우저 탭에서 클러스터 대시보드를 열어봅니다.

* SSH 자격 증명을 회수할 수 없는 경우 [AMBARI UI 내에서 자격 증명을 다시 설정할](../hdinsight-administer-use-portal-linux.md#change-passwords)수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.