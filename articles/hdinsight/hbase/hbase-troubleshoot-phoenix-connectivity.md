---
title: Azure HDInsight의 Apache Phoenix 연결 문제
description: Azure HDInsight에서 Apache HBase와 Apache Phoenix 간의 연결 문제
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887294"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Phoenix 연결 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Phoenix를 사용 하 여 Apache HBase에 연결할 수 없습니다. 이유는 다를 수 있습니다.

## <a name="cause-incorrect-ip"></a>원인: 잘못 된 IP

활성 사육 아웃 노드의 IP가 잘못 되었습니다.

### <a name="resolution"></a>해상도

Ambari UI에서 활성 사육 사 노드의 IP를 식별할 수 있습니다 .이는 **HBase** > **빠른 링크** > **zk (활성)**  > **사육 아웃 정보**입니다. 필요에 따라 IP를 수정 합니다.

---

## <a name="cause-systemcatalog-table-offline"></a>원인: 시스템. 오프 라인 카탈로그 테이블

`!tables`와 같은 명령을 실행 하면 다음과 비슷한 오류 메시지가 표시 됩니다.

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

`count 'SYSTEM.CATALOG'`와 같은 명령을 실행 하면 다음과 비슷한 오류 메시지가 표시 됩니다.

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>해상도

Apache Ambari UI에서 다음 단계를 완료 하 여 모든 사육 드 노드에서 HMaster 서비스를 다시 시작 합니다.

1. HBase의 **요약** 섹션에서 **hbase** > **활성 HBase Master**로 이동 합니다.

1. **구성 요소** 섹션에서 HBase Master 서비스를 다시 시작 합니다.

1. 나머지 모든 **Standby HBase Master** 서비스에 대해 이러한 단계를 반복합니다.

HBase Master 서비스가 안정화 되 고 복구를 완료 하는 데 최대 5 분이 걸릴 수 있습니다. `SYSTEM.CATALOG` 테이블이 정상적으로 다시 시작 되 면 Apache Phoenix에 대 한 연결 문제가 자동으로 해결 됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
