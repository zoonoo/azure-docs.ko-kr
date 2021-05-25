---
title: Azure HDInsight에서 Apache HBase를 쿼리하는 REST API
description: 이 문서에서는 Azure HDInsight 클러스터에서 Apache HBase 구성 요소와 상호 작용할 때의 문제 해결 단계를 설명합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942882"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase를 쿼리하는 REST API

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache HBase REST 인터페이스를 사용하여 기본값이 아닌 네임스페이스에서 테이블을 쿼리하면 런타임 오류가 발생합니다(HTTP 상태 500).

## <a name="cause"></a>원인

HBase REST API는 기본 네임스페이스를 사용하는 경우에만 지원됩니다. 이는 HBase 네임스페이스를 사용하거나 HDInsight의 REST 서버가 포함된 열 패밀리가 있는 열에서 특정 GET를 참조하는 호출과 관련된 알려진 문제입니다. 이는 HDInsight 게이트웨이의 보안 문제로 인해 발생합니다. API를 사용하여 네임스페이스가 있는 테이블을 만들고 열 패밀리를 통해 열에 액세스하는 경우 IIS 게이트웨이 모듈에서 보안 문제로 간주되는 `:` 문자를 지정해야 합니다.

## <a name="mitigation"></a>완화 방법

HDInsight 클러스터와 동일한 Azure VNet에 있는 VM에 애플리케이션을 배포하여 게이트웨이/REST 서버를 우회합니다. 그런 다음, RPC를 통해 직접 HBase와 통신(REST 서버를 완전히 우회)하거나 HDInsight 게이트웨이를 우회하는 작업자 노드에서 실행되는 개별 REST 서버와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.