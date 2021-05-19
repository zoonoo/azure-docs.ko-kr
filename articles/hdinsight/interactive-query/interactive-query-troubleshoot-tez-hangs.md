---
title: Azure HDInsight에서 Apache Tez 애플리케이션 중단
description: Azure HDInsight에서 Apache Tez 애플리케이션 중단
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935937"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Tez 애플리케이션 중단

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Hive 작업을 제출한 후 Tez 보기에서 작업 상태는 "실행 중" 이지만 진행 중인 것으로 표시되지 않습니다.

## <a name="cause"></a>원인

너무 많은 작업을 제출했습니다. 긴 Yarn 큐입니다.

## <a name="resolution"></a>해결 방법

클러스터를 확장하거나 Yarn 큐가 드레이닝될 때까지 기다립니다.

기본값으로 `yarn.scheduler.capacity.maximum-applications`은 실행 중이거나 보류 중인 애플리케이션의 최대 수를 제어하며 이는 기본값으로 `10000`로 설정됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.