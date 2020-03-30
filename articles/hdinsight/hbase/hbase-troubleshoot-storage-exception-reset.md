---
title: Azure HDInsight에서 연결 재설정 후 저장소 예외
description: Azure HDInsight에서 연결 재설정 후 저장소 예외
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887226"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 연결 재설정 후 저장소 예외

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

새 아파치 HBase 테이블을 만들 수 없습니다.

## <a name="cause"></a>원인

테이블 잘림 프로세스 중에 저장소 연결 문제가 발생했습니다. HBase 메타데이터 테이블에서 테이블 항목이 삭제되었습니다. 하나의 Blob 파일을 제외한 모든 파일이 삭제되었습니다.

저장소에 앉아 라는 `/hbase/data/default/ThatTable` 폴더 Blob 이 없었지만. WASB 드라이버는 위의 Blob 파일이 있는지 를 발견했으며 부모 폴더가 `/hbase/data/default/ThatTable` 존재한다고 가정했기 때문에 호출된 Blob을 만들 수 없으므로 테이블을 만드는 데 실패합니다.

## <a name="resolution"></a>해결 방법

1. 아파치 암바리 UI에서 활성 HMaster를 다시 시작합니다. 이렇게 하면 두 대기 HMaster 중 하나가 활성 HMaster가 되고 새 활성 HMaster가 메타데이터 테이블 정보를 다시 로드합니다. 따라서 HMaster UI에서 `already-deleted` 테이블을 볼 수 없습니다.

1. 클라우드 탐색기 와 같은 UI 도구또는 와 같은 `hdfs dfs -ls /xxxxxx/yyyyy`명령을 실행하는 UI 도구에서 고아 Blob 파일을 찾을 수 있습니다. 실행하여 `hdfs dfs -rmr /xxxxx/yyyy` 해당 Blob을 삭제합니다. `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`)을 입력합니다.

이제 HBase에서 이름이 같은 새 테이블을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
