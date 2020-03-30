---
title: 액세스 중인 계정은 Azure HDInsight에서 http 오류를 지원하지 않습니다.
description: 이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165554"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>액세스 중인 계정은 Azure HDInsight에서 http 오류를 지원하지 않습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

다음과 같은 오류 메시지가 수신됩니다.

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>원인

오류 메시지가 수신되는 데는 여러 가지 이유가 있습니다.

* 저장소 계정에 [보안 전송이](../../storage/common/storage-require-secure-transfer.md) 활성화되어 있으며 잘못된 [URI 체계가](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 사용중입니다.

* 보안 전송이 *비활성화된*저장소 계정으로 클러스터가 만들어졌습니다. 그 후 저장소 계정에서 보안 전송이 활성화되었습니다.

## <a name="resolution"></a>해결 방법

Azure 저장소 또는 데이터 레이크 저장소 Gen2에 대해 보안 `wasbs://` `abfss://`전송을 사용하도록 설정하면 URI가 각각 또는  [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.

새 클러스터의 경우 원하는 보안 전송 설정이 이미 있는 저장소 계정을 사용합니다. 기존 클러스터에서 사용 중이면 저장소 계정의 보안 전송 설정을 변경하지 마십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
