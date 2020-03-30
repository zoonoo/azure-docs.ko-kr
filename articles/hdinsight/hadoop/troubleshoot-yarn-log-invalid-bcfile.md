---
title: Azure HDInsight에서 아파치 원사 로그인을 읽을 수 없음
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 해결 가능성
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776197"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 아파치 원사 로그를 읽을 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

저장소 계정에서 찾은 아파치 원사 로그는 사람이 읽을 수 없습니다. 파일 파서는 작동하지 않으며 다음과 같은 오류 메시지가 생성됩니다.

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>원인

아파치 원사 로그는 `IndexFile` 파일 파서에 의해 지원되지 않는 형식으로 집계됩니다.

## <a name="resolution"></a>해결 방법

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net`의 `CLUSTERNAME` 이름으로 이동합니다.

1. Ambari UI에서 **YARN** > **Configs** > **고급** > 고급**원사 사이트로**이동합니다.

1. WASB 저장소의 경우: `yarn.log-aggregation.file-formats` 의 `IndexedFormat,TFile`기본값은 입니다. 값을 로 `TFile`변경합니다.

1. ADLS 저장소의 경우: `yarn.nodemanager.log-aggregation.compression-type` 의 `gz`기본값은 입니다. 값을 로 `none`변경합니다.

1. 변경 된 시간을 저장 하 고 모든 영향을 받는 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
