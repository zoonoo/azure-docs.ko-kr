---
title: 아파치 스파크 스트리밍 응용 프로그램 Azure HDInsight에서 24 일 후 중지
description: 아파치 스파크 스트리밍 응용 프로그램은 24 일 동안 실행 한 후 중지하고 로그 파일에 오류가 없습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894427"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>시나리오: 아파치 스파크 스트리밍 응용 프로그램 Azure HDInsight에서 24 일 동안 실행 후 중지

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 스파크 스트리밍 응용 프로그램은 24 일 동안 실행 한 후 중지하고 로그 파일에 오류가 없습니다.

## <a name="cause"></a>원인

이 `livy.server.session.timeout` 값은 아파치 리비가 세션이 완료될 때까지 기다려야 하는 길이를 제어합니다. 세션 길이가 값에 `session.timeout` 도달하면 Livy 세션과 응용 프로그램이 자동으로 죽게 됩니다.

## <a name="resolution"></a>해결 방법

장기 실행 작업의 경우 Ambari UI 사용 의 `livy.server.session.timeout` 가치를 높입니다. URL을 `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`사용하여 Ambari UI에서 Livy 구성에 액세스할 수 있습니다.

포털에 표시된 HDInsight 클러스터의 이름으로 바꿉니다. `<yourclustername>`

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
