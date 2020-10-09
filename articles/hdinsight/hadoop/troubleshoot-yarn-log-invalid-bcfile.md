---
title: Azure HDInsight에서 Apache Yarn 로그를 읽을 수 없습니다.
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776197"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Yarn log를 읽을 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

저장소 계정에서 찾은 Apache Yarn 로그는 사람이 읽을 수 없습니다. 파일 파서가 작동 하지 않고 다음 오류 메시지를 생성 합니다.

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>원인

Apache Yarn log는 `IndexFile` 파일 파서에서 지원 하지 않는 형식으로 집계 됩니다.

## <a name="resolution"></a>해결 방법

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. Ambari UI에서 **YARN**  >  **Configs**  >  **advanced**  >  **advanced YARN-site**로 이동 합니다.

1. WASB 저장소의 경우:의 기본값은 `yarn.log-aggregation.file-formats` 입니다 `IndexedFormat,TFile` . 값을로 변경 `TFile` 합니다.

1. ADLS 저장소의 경우:의 기본값은 `yarn.nodemanager.log-aggregation.compression-type` 입니다 `gz` . 값을로 변경 `none` 합니다.

1. 변경 내용을 저장 하 고 영향을 받는 모든 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
