---
title: Apache Hive의 조인으로 인해 OutOfMemory 오류 발생 - Azure HDInsight
description: OutOfMemory 오류 "GC 오버헤드 한도 초과 오류" 처리
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935950"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive의 조인으로 인해 OutOfMemory 오류 발생

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Hive 조인의 기본값 동작은 맵/감소 단계를 수행하지 않고도 조인을 수행할 수 있도록 테이블의 전체 콘텐츠를 메모리로 로드하는 것입니다. Hive 테이블이 너무 커서 메모리에 맞지 않는 경우 쿼리가 실패할 수 있습니다.

## <a name="cause"></a>원인

충분한 크기의 하이브에서 조인을 실행하는 경우 다음과 같은 오류가 발생합니다.

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>해결 방법

Hive 구성 값을 다음과 같이 설정하여 Hive가 조인 시 (맵/감소 단계를 수행하는 대신) 테이블을 메모리에 로드하지 않도록 방지합니다.

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>다음 단계

이 값을 설정해도 문제가 해결되지 않으면 다음 중 하나를 수행하세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.