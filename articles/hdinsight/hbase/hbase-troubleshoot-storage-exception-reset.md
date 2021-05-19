---
title: Azure HDInsight에서 연결을 초기화한 후의 스토리지 예외
description: Azure HDInsight에서 연결을 초기화한 후의 스토리지 예외
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936754"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 연결을 초기화한 후의 스토리지 예외

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

새 Apache HBase 테이블을 만들 수 없습니다.

## <a name="cause"></a>원인

테이블 잘림 프로세스가 진행되는 동안 스토리지 연결 문제가 발생했습니다. HBase 메타데이터 테이블에서 테이블 항목이 삭제되었습니다. Blob 파일이 하나만 남고 모두 삭제되었습니다.

스토리지에 `/hbase/data/default/ThatTable`이라는 폴더 blob이 없습니다. WASB 드라이버는 위에 있는 blob 파일의 존재를 발견했으며 `/hbase/data/default/ThatTable`이라는 blob을 만들 수 없습니다. 부모 폴더를 사용하는 것으로 가정했으므로 테이블을 만드는 작업이 실패합니다.

## <a name="resolution"></a>해결 방법

1. Apache Ambari UI에서 활성 HMaster를 다시 시작합니다. 이렇게 하면 두 개의 대기 HMaster 중 하나가 활성 상태가 되고 새 활성 HMaster가 메타데이터 테이블 정보를 다시 로드합니다. 따라서 HMaster UI의 `already-deleted` 테이블은 표시되지 않습니다.

1. 클라우드 탐색기 또는 `hdfs dfs -ls /xxxxxx/yyyyy`와 같은 명령을 실행하는 등의 UI 도구에서 연결 없는 blob 파일을 찾을 수 있습니다. `hdfs dfs -rmr /xxxxx/yyyy`을 실행하여 해당 blob을 삭제합니다. 예들 들어 `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`입니다.

이제 HBase에서 동일한 이름을 사용하여 새 테이블을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.