---
title: 액세스되는 계정은 Azure HDInsight의 http 오류를 지원하지 않습니다.
description: 이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943081"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>액세스되는 계정은 Azure HDInsight의 http 오류를 지원하지 않습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

다음 오류 메시지가 수신됩니다.

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>원인

오류 메시지가 수신되는 이유는 여러 가지가 있습니다.

* 스토리지 계정에 [보안 전송](../../storage/common/storage-require-secure-transfer.md)이 활성화되어 있으며 잘못된 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)가 사용되고 있습니다.

* 보안 전송이 *비활성화* 된 스토리지 계정을 사용하여 클러스터가 생성되었습니다. 이후 스토리지 계정에서 보안 전송이 활성화되었습니다.

## <a name="resolution"></a>해결 방법

Azure Storage 또는 Data Lake Storage Gen2에 보안 전송을 사용하는 경우 URI는 각각 `wasbs://` 또는 `abfss://`가 됩니다.  [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.

새 클러스터의 경우 원하는 보안 전송 설정이 이미 있는 스토리지 계정을 사용합니다. 기존 클러스터에서 사용 중인 스토리지 계정에 대한 보안 전송 설정을 변경하지 마세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.