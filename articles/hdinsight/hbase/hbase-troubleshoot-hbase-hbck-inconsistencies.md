---
title: Azure HDInsight에서 hbase hbck의 불일치를 반환 합니다.
description: Azure HDInsight에서 hbase hbck의 불일치를 반환 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 6bf136d943f230eb02df62cbaa732e28d6b359a4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968463"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>시나리오: `hbase hbck` 명령이 Azure HDInsight에서 불일치를 반환 합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue-region-is-not-in-hbasemeta"></a>문제: 지역이 다음에 있지 않음`hbase:meta`

지역 xxx (HDFS의 경우), 모든 `hbase:meta` 지역 서버에 나열 되거나 배포 되지 않습니다.

### <a name="cause"></a>원인

잠기기.

### <a name="resolution"></a>해결 방법

1. 다음을 실행 하 여 meta 테이블을 수정 합니다.

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 다음을 실행 하 여 영역 서버에 영역을 할당 합니다.

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>문제: 지역이 오프 라인 상태임

Xxx 지역이 영역 서버에 배포 되지 않았습니다. 즉 `hbase:meta`, 지역이 오프 라인 상태입니다.

### <a name="cause"></a>원인

잠기기.

### <a name="resolution"></a>해결 방법

다음을 실행 하 여 영역을 온라인 상태로 만들기:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>문제: 영역에 동일한 시작/종료 키가 있습니다.

### <a name="cause"></a>원인

잠기기.

### <a name="resolution"></a>해결 방법

이러한 겹치는 영역을 수동으로 병합 합니다. HBase HMaster 웹 UI 테이블 섹션으로 이동 하 여 문제가 있는 테이블 링크를 선택 합니다. 해당 테이블에 속하는 각 지역의 시작 키/끝 키가 표시 됩니다. 그런 다음 이러한 겹치는 영역을 병합 합니다. HBase shell에서를 수행 `merge_region 'xxxxxxxx','yyyyyyy', true`합니다. 예를 들어:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

이 시나리오에서는 영역 a와 영역 c를 병합 하 고 영역 b와 동일한 키 범위를 사용 하 여 영역 d를 가져온 다음 영역 b와 영역 d를 병합 해야 합니다. xxxxxxx 및 yyyyyy는 각 지역 이름의 끝에 있는 해시 문자열입니다. 두 개의 불연속 영역을 병합 하지 않도록 주의 해야 합니다. Merge A 및 C와 같이 각 병합 후에는 HBase에서 지역 d에 대 한 압축을 시작 합니다. 다른 지역으로 병합을 수행 하기 전에 압축이 완료 될 때까지 기다립니다. HBase HMaster UI에서 해당 지역 서버 페이지에 대 한 압축 상태를 찾을 수 있습니다.

---

## <a name="issue-cant-load-regioninfo"></a>문제: 로드할 수 없음`.regioninfo`

`.regioninfo` 영역`/hbase/data/default/tablex/regiony`에 대해 로드할 수 없습니다.

### <a name="cause"></a>원인

영역 서버가 충돌 하거나 VM을 다시 부팅 하는 경우 영역 부분 삭제 때문일 수 있습니다. 현재 Azure Storage는 플랫 blob 파일 시스템 이며 일부 파일 작업은 원자성이 아닙니다.

### <a name="resolution"></a>해결 방법

다음 남은 파일 및 폴더를 수동으로 정리:

1. 을 `hdfs dfs -ls /hbase/data/default/tablex/regiony` 실행 하 여 아직 아래에 있는 폴더/파일을 확인 합니다.

1. 모든 `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` 자식 파일/폴더를 삭제 하려면 실행 합니다.

1. 을 `hdfs dfs -rmr /hbase/data/default/tablex/regiony` 실행 하 여 region 폴더를 삭제 합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
