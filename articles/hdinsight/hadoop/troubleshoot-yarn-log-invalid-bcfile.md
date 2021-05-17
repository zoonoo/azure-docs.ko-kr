---
title: Azure HDInsight에서 Apache Yarn 로그를 읽을 수 없습니다.
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943046"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Yarn 로그를 읽을 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

스토리지 계정에서 찾은 Apache Yarn 로그는 사람이 읽을 수 없습니다. 파일 파서가 작동하지 않고 다음 오류 메시지가 생성됩니다.

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>원인

Apache Yarn 로그는 파일 파서에서 지원하지 않는 `IndexFile` 형식으로 집계됩니다.

## <a name="resolution"></a>해결 방법

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. Ambari UI에서 **YARN** > **구성** > **고급** > **고급 YARN 사이트** 로 이동합니다.

1. WASB 스토리지의 경우: `yarn.log-aggregation.file-formats`의 기본값은 `IndexedFormat,TFile`입니다. 값을 `TFile`로 변경합니다.

1. ADLS 스토리지의 경우: `yarn.nodemanager.log-aggregation.compression-type`의 기본값은 `gz`입니다. 값을 `none`으로 변경합니다.

1. 변경 내용을 저장하고 영향을 받은 모든 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.