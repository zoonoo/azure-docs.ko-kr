---
title: Azure HDInsight의 Apache Ambari Hive 보기에서 쿼리를 실행할 때 예외 발생
description: Azure HDInsight의 Apache Ambari Hive 뷰를 통해 Apache Hive 쿼리를 실행할 때의 문제 해결 단계입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933004"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari Hive 보기에서 쿼리를 실행할 때 예외 발생

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Ambari Hive 보기에서 Apache Hive 쿼리를 실행하는 경우 간헐적으로 다음 오류 메시지가 표시됩니다.

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>원인

게이트웨이 시간 초과

게이트웨이 시간 초과 값은 2분입니다. Ambari Hive 보기의 쿼리는 게이트웨이를 통해 `/hive2` 엔드포인트로 전송됩니다. 쿼리가 성공적으로 컴파일되고 수락되면 HiveServer는 `queryid`를 반환합니다. 그러면 클라이언트가 쿼리 상태에 대한 폴링을 유지합니다. 이 프로세스 동안 HiveServer가 2분 내에 HTTP 응답을 반환하지 않으면 HDI 게이트웨이는 호출자에게 502.3 게이트웨이 시간 초과 오류를 발생시킵니다. 쿼리를 처리하기 위해 제출할 때(가능성 높음)와 가져오기 상태 호출 시에도(가능성 낮음) 오류가 발생할 수 있습니다. 사용자는 둘 중 하나를 볼 수 있습니다.

http 처리기 스레드는 빠르기 때문에 작업을 준비하고 `queryid`를 반환합니다. 그러나 몇 가지 이유로 인해 모든 처리기 스레드가 사용 중이어서 새 쿼리와 가져오기 상태 호출에 대한 시간 초과가 발생할 수 있습니다.

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 처리기 스레드의 책임

클라이언트가 HiveServer에 쿼리를 제출하면 전경 스레드에서 다음을 수행합니다.

* 요청 구문 분석, 의미 확인 수행
* 잠금 가져오기
* 필요한 경우 메타스토어 조회
* 쿼리(DDL 또는 DML) 컴파일
* 쿼리 계획 준비
* 권한 부여 수행(보안 클러스터에서 적용 가능한 모든 Ranger 정책 실행)

## <a name="resolution"></a>해결 방법

상황을 개선할 수 있는 몇 가지 일반적인 권장 사항은 다음과 같습니다.

* 외부 하이브 메타스토어를 사용하는 경우 DB 메트릭을 확인하고 데이터베이스가 오버로드되지 않았는지 확인합니다. 메타스토어 데이터베이스 레이어의 크기를 조정하는 것이 좋습니다.

* 병렬 ops가 설정되어 있는지 확인합니다(이렇게 하면 HTTP 처리기 스레드를 병렬로 실행할 수 있음). 값을 확인하려면 [Apache Ambari](../hdinsight-hadoop-manage-ambari.md)를 시작하고 **Hive** > **구성** > **고급** > **사용자 지정 하이브 사이트** 로 이동합니다. `hive.server2.parallel.ops.in.session`에 대한 값은 `true`여야 합니다.

* 클러스터의 VM SKU가 부하에 비해 너무 작지 않은지 확인하십시오. 여러 클러스터 간에 작업을 분할하는 것이 좋습니다. 자세한 내용은 [클러스터 형식 선택](../hdinsight-capacity-planning.md#choose-a-cluster-type)을 참조하세요.

* 클러스터에 Ranger가 설치된 경우 각 쿼리에 대해 평가해야 하는 Ranger 정책이 너무 많은지 확인하세요. 중복되거나 불필요한 정책을 찾습니다.

* Ambari에서 **HiveServer2 힙 크기** 값을 확인합니다. **Hive** > **구성** > **설정** > **최적화** 로 이동합니다. 값이 10GB보다 큰지 확인합니다. 성능을 최적화하기 위해 필요에 따라 조정합니다.

* Hive 쿼리가 잘 조정되었는지 확인합니다. 자세한 내용은 [Azure HDInsight에서 Apache Hive 쿼리 최적화](../hdinsight-hadoop-optimize-hive-query.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.