---
title: Azure HDInsight에서 Zeppelin에 로그인 할 수 없습니다.
description: Azure HDInsight에서 Zeppelin에 로그인 할 수 없습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: d4bb9e090b238eacec77f4c19bbf9afb3e09a912
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091057"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Zeppelin에 로그인 할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Active directory에서 암호 추가를 변경한 후에는 Apache Zeppelin에 로그인 할 수 없습니다.

## <a name="cause"></a>원인

`activeDirectoryRealm.systemUsername` 파일의에서 언급 한 사용자가 active directory 암호를 변경 했습니다. `shiro_ini`

## <a name="resolution"></a>해결 방법

1. Ambari의 Zeppelin `activeDirectoryRealm.systemPassword = <new password>` `shiro_ini` config에를 포함 하 여 변경 된 암호가 근본 원인 인지 확인 합니다. `activeDirectoryRealm.hadoopSecurityCredentialPath` 설정을 제거 합니다. 아래는의 `shiro ini`위치입니다.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. 이제 사용자가 1 단계 후에 Zeppelin에 로그인 할 수 있는 경우 새 `jceks` 암호를 사용 하 여 새 파일을 `activeDirectoryRealm.hadoopSecurityCredentialPath` 만들고를 새 파일로 바꿉니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
