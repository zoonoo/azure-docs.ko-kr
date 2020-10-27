---
title: Apache Hive에서 조인 하 여 OutOfMemory 오류 발생-Azure HDInsight
description: OutOfMemory 오류 처리 "GC 오버 헤드 제한을 초과 했습니다."
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c227758e31b3b17768b8140475872245b2f34e52
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532954"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive의 조인으로 인해 OutOfMemory 오류가 발생 합니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive 조인의 기본 동작은 맵/감소 단계를 수행 하지 않고도 조인을 수행할 수 있도록 테이블의 전체 내용을 메모리로 로드 하는 것입니다. Hive 테이블이 너무 커서 메모리에 맞지 않는 경우 쿼리가 실패할 수 있습니다.

## <a name="cause"></a>원인

충분 한 크기의 hive에서 조인을 실행 하는 경우 다음과 같은 오류가 발생 합니다.

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>해결 방법

Hive 구성 값을 설정 하 여 Hive가 조인 시 테이블을 메모리에 로드 하는 것을 방지 합니다 (맵/감소 단계를 수행 하는 대신).

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>다음 단계

이 값을 설정 해도 문제가 해결 되지 않으면 다음 중 하나를 수행 하세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.