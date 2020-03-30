---
title: hbase hbck Azure HDInsight에서 불일치를 반환합니다.
description: hbase hbck Azure HDInsight에서 불일치를 반환합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887328"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>시나리오: `hbase hbck` Azure HDInsight에서 명령 반환 불일치

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue-region-is-not-in-hbasemeta"></a>문제: 지역이`hbase:meta`

HDFS의 지역 xxx이지만 모든 `hbase:meta` 리전 서버에 나열되거나 배포되지 는 않습니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

1. 다음을 실행하여 메타 테이블을 수정합니다.

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 다음을 실행하여 RegionServers에 지역 할당

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>문제: 지역이 오프라인 상태입니다.

지역 xxx는 모든 RegionServer에 배포되지 않습니다. 즉, 지역이 `hbase:meta`에 있지만 오프라인 상태입니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

다음을 실행하여 지역을 온라인 으로 가져오십시오.

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>문제: 리전이 시작/종료 키와 동일합니다.

### <a name="cause"></a>원인

다양함

### <a name="resolution"></a>해결 방법

겹친 영역을 수동으로 병합합니다. HBase HMaster 웹 UI 테이블 섹션으로 이동하여 문제가 있는 테이블 링크를 선택합니다. 해당 테이블에 속한 각 지역의 시작 키/종료 키가 표시됩니다. 그런 다음 겹친 영역을 병합합니다. HBase 셸에서 `merge_region 'xxxxxxxx','yyyyyyy', true`수행합니다. 예를 들어:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

이 시나리오에서는 RegionA 및 RegionC를 병합 하 고 RegionB와 동일한 키 범위로 RegionD를 가져옵니다. xxxxxxx 및 yyyyyy는 각 지역 이름의 끝에 있는 해시 문자열입니다. 두 개의 불연속 영역을 병합하지 않도록 주의하십시오. 병합 A 및 C와 같은 각 병합 후 HBase는 RegionD에서 압축을 시작합니다. 지역D와 다른 병합을 수행하기 전에 압축이 완료될 때까지 기다립니다. HBase HMaster UI에서 해당 지역 서버 페이지에서 압축 상태를 찾을 수 있습니다.

---

## <a name="issue-cant-load-regioninfo"></a>문제: 로드할 수 없습니다.`.regioninfo`

지역에 `/hbase/data/default/tablex/regiony`대 `.regioninfo` 한 로드할 수 없습니다.

### <a name="cause"></a>원인

이는 RegionServer가 충돌하거나 VM이 재부팅될 때 지역 부분 삭제로 인해 발생할 가능성이 큽니다. 현재 Azure Storage는 플랫 Blob 파일 시스템이며 일부 파일 작업은 원자성되지 않습니다.

### <a name="resolution"></a>해결 방법

이러한 나머지 파일 및 폴더를 수동으로 정리합니다.

1. 실행하여 `hdfs dfs -ls /hbase/data/default/tablex/regiony` 폴더/파일이 아직 아래에 있는지 확인합니다.

1. 실행하여 `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` 모든 하위 파일/폴더 삭제

1. 실행하여 `hdfs dfs -rmr /hbase/data/default/tablex/regiony` 영역 폴더를 삭제합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
