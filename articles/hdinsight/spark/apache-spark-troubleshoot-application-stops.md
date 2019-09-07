---
title: Azure HDInsight에서 24 일 후에 스트리밍 응용 프로그램이 중지 Apache Spark
description: 24 일간 실행 한 후 Apache Spark 스트리밍 응용 프로그램이 중지 되 고 로그 파일에 오류가 없습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: c513c5df0d83eb0049683f88d85e8a1c41fd0bf0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736287"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 24 일간 실행 한 후 스트리밍 응용 프로그램이 중지 Apache Spark

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

24 일간 실행 한 후 Apache Spark 스트리밍 응용 프로그램이 중지 되 고 로그 파일에 오류가 없습니다.

## <a name="cause"></a>원인

이 `livy.server.session.timeout` 값은 Apache Livy가 세션이 완료 될 때까지 대기 해야 하는 시간을 제어 합니다. 세션 길이가 값에 `session.timeout` 도달 하면 Livy 세션과 응용 프로그램이 자동으로 종료 됩니다.

## <a name="resolution"></a>해결 방법

장기 실행 작업의 경우 Ambari UI를 `livy.server.session.timeout` 사용 하 여 값을 늘립니다. URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`을 사용 하 여 Ambari UI에서 Livy 구성에 액세스할 수 있습니다.

을 `<yourclustername>` 포털에 표시 된 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
