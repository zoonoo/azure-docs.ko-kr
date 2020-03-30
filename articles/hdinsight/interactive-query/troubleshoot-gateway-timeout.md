---
title: Azure HDInsight에서 아파치 암바리 하이브 보기에서 쿼리를 실행할 때 예외
description: Azure HDInsight에서 아파치 암바리 하이브 뷰를 통해 아파치 하이브 쿼리를 실행할 때 문제 해결 단계.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895044"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Azure HDInsight에서 아파치 암바리 하이브 보기에서 쿼리를 실행할 때 예외

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 암바리 하이브 보기에서 아파치 하이브 쿼리를 실행 하는 경우 간헐적으로 다음과 같은 오류 메시지가 나타납니다.

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>원인

게이트웨이 시간 입니다.

게이트웨이 시간 초과 값은 2분입니다. Ambari Hive 보기의 쿼리는 `/hive2` 게이트웨이를 통해 끝점으로 전송됩니다. 쿼리가 성공적으로 컴파일되고 수락되면 HiveServer는 `queryid`을 반환합니다. 그런 다음 클라이언트는 쿼리 상태에 대한 폴링을 유지합니다. 이 과정에서 HiveServer가 2분 이내에 HTTP 응답을 반환하지 않으면 HDI 게이트웨이는 호출자에게 502.3 게이트웨이 시간 초과 오류를 throw합니다. 이 오류는 쿼리가 처리를 위해 제출될 때(더 높으며) 상태 호출(덜 가능성)에서 발생할 수 있습니다. 사용자는 둘 중 하나를 볼 수 있습니다.

http 처리기 스레드는 작업을 준비하고 `queryid`을 반환하는 빠른 것으로 간주됩니다. 그러나 여러 가지 이유로 인해 모든 처리기 스레드가 사용 중일 수 있으므로 새 쿼리및 get 상태 호출에 대한 시간 시간이 발생할 수 있습니다.

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 처리기 스레드의 책임

클라이언트가 HiveServer에 쿼리를 제출하면 전경 스레드에서 다음을 수행합니다.

* 요청을 구문 분석하고 의미 체계 확인을 수행합니다.
* 잠금 획득
* 필요한 경우 메타스토어 조회
* 쿼리 컴파일(DDL 또는 DML)
* 쿼리 계획 준비
* 권한 부여 수행(보안 클러스터에서 적용 가능한 모든 레인저 정책 실행)

## <a name="resolution"></a>해결 방법

상황을 개선하기 위해 다음과 같은 몇 가지 일반적인 권장 사항을 제시합니다.

* 외부 하이브 메타스토어를 사용하는 경우 DB 메트릭을 확인하고 데이터베이스가 오버로드되지 않았는지 확인합니다. 메타스토어 데이터베이스 계층의 크기를 조정하는 것이 좋습니다.

* 병렬 작전이 켜져 있는지 확인합니다(이렇게 하면 HTTP 처리기 스레드가 병렬로 실행될 수 있음). 값을 확인하려면 [아파치 암바리를](../hdinsight-hadoop-manage-ambari.md) 시작하고 **하이브** > **구성** > **고급** > **사용자 정의 하이브 사이트로**이동합니다. 의 값은 `hive.server2.parallel.ops.in.session` `true`이어야 합니다.

* 클러스터의 VM SKU가 부하에 너무 작지 않은지 확인합니다. 작업을 여러 클러스터로 분할하는 것이 좋습니다. 자세한 내용은 [클러스터 유형 선택을](../hdinsight-capacity-planning.md#choose-a-cluster-type)참조하십시오.

* 레인저가 클러스터에 설치된 경우 각 쿼리에 대해 평가해야 하는 레인저 정책이 너무 많은지 확인하십시오. 중복또는불필요한정책을찾습니다.

* Ambari의 **HiveServer2 힙 크기** 값을 확인합니다. **하이브** > **구성** > **설정** > **최적화로**이동합니다. 값이 10GB보다 큰지 확인합니다. 필요에 따라 조정하여 성능을 최적화합니다.

* Hive 쿼리가 잘 조정되었는지 확인합니다. 자세한 내용은 [Azure HDInsight의 아파치 하이브 쿼리 최적화를](../hdinsight-hadoop-optimize-hive-query.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
