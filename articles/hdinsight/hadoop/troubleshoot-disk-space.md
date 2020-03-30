---
title: Azure HDInsight의 디스크 공간 관리
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 해결 가능성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473013"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Azure HDInsight의 디스크 공간 관리

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="hive-log-configurations"></a>하이브 로그 구성

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net`의 `CLUSTERNAME` 이름으로 이동합니다.

1. **하이브** > **구성** > **고급** > **하이브-log4j로**이동합니다. 다음 설정을 검토합니다.

    * `hive.root.logger=DEBUG,RFA`. 이 값은 기본값으로 [로그 수준을](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) 수정하여 `INFO` 로그 항목을 적게 인쇄합니다.

    * `log4jhive.log.maxfilesize=1024MB`. 원하는 대로 수정하는 기본값입니다.

    * `log4jhive.log.maxbackupindex=10`. 원하는 대로 수정하는 기본값입니다. 매개 변수가 생략된 경우 생성된 로그 파일은 끝이 없습니다.

## <a name="yarn-log-configurations"></a>원사 로그 구성

다음 구성을 검토합니다.

* Apache Ambari

    1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net`의 `CLUSTERNAME` 이름으로 이동합니다.

    1. **하이브** > **구성** > **고급** > **리소스 관리자로**이동합니다. **로그 집계 활성화가** 선택되었는지 확인합니다. 비활성화된 경우 이름 노드는 로그를 로컬로 유지하고 응용 프로그램 완료 또는 종료 시 원격 저장소에 집계하지 않습니다.

* 클러스터 크기가 워크로드에 적합한지 확인합니다. 워크로드가 최근에 변경되었거나 클러스터의 크기가 조정되었을 수 있습니다. 클러스터를 [확장하여](../hdinsight-scaling-best-practices.md) 더 높은 워크로드에 맞게 조정합니다.

* `/mnt/resource`리소스 관리자가 다시 시작되는 경우와 같이 분리된 파일로 채워질 수 있습니다. 필요한 경우 수동으로 `/mnt/resource/hadoop/yarn/log` 청소하고 `/mnt/resource/hadoop/yarn/local`.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
