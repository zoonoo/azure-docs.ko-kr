---
title: Azure HDInsight의 스크립트 작업 문제 해결
description: Azure HDInsight의 스크립트 작업에 대 한 일반적인 문제 해결 단계입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 977e3571a24e8be9d9ef6cd79e80e654ca944fa4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538819"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Azure HDInsight의 스크립트 작업 문제 해결

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="viewing-logs"></a>로그 보기

Apache Ambari 웹 UI를 사용 하 여 스크립트 동작에 의해 기록 된 정보를 볼 수 있습니다. 클러스터를 만드는 동안 스크립트가 실패 하는 경우 로그는 기본 클러스터 저장소 계정에 있습니다. 이 섹션에서는 이 두 옵션을 모두 사용하여 로그를 검색하는 방법에 대한 정보를 제공합니다.

### <a name="apache-ambari-web-ui"></a>Apache Ambari 웹 UI

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. 페이지 위쪽의 모음에서 **작업** 항목을 선택합니다. Ambari를 통해 클러스터에서 수행된 현재 및 이전 작업이 목록에 표시됩니다.

    ![선택한 작업으로 Ambari 웹 UI 모음](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. **작업** 열에 **run\_customscriptaction** 이 있는 항목을 찾습니다. 이러한 항목을 스크립트 동작을 실행할 때 생성됩니다.

    ![Apache Ambari 스크립트 동작 작업](./media/troubleshoot-script-action/ambari-script-action.png)

    **STDOUT** 및 **STDERR** 출력을 보려면 **run\customscriptaction** 항목을 선택하고 링크를 통해 드릴다운합니다. 이 출력은 스크립트가 실행될 때 생성되며, 여기에는 유용한 정보가 있을 수 있습니다.

### <a name="default-storage-account"></a>기본 스토리지 계정

스크립트 오류로 인해 클러스터를 만드는 데 실패하면 해당 로그가 클러스터 스토리지 계정에 보관됩니다.

* 스토리지 로그는 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`에서 지원됩니다.

    ![스크립트 동작 로그](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    이 디렉터리에서 로그는 **헤드 노드** , **작업자 노드** 및 **Zookeeper 노드** 에 대해 별도로 구성됩니다. 다음 예제를 참조하세요.

    * **헤드 노드** : `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **작업자 노드** : `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **사육 아웃 노드** : `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* 해당 호스트의 모든 **stdout** 및 **stderr** 이 스토리지 계정에 업로드됩니다. 각 스크립트 동작에 대해 **output-\*.txt** 및 **errors-\*.txt** 가 하나씩 있습니다. **output-*.txt** 파일에는 호스트에서 실행된 스크립트의 URI 정보를 포함되어 있습니다. 다음 텍스트는 이 정보의 예제입니다.

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* 같은 이름으로 반복해서 스크립트 작업 클러스터를 만들 수 있습니다. 이 경우 **날짜** 폴더 이름에 따라 관련 로그를 구분할 수 있습니다. 예를 들어 서로 다른 날짜에 만든 클러스터( **mycluster** )의 폴더 구조는 다음 로그 항목과 비슷하게 나타납니다.

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 같은 날 같은 이름으로 스크립트 작업 클러스터를 만든 경우 고유의 접두사를 사용하여 관련 로그 파일을 식별할 수 있습니다.

* 오전 12시(자정)쯤에 클러스터를 만든 경우 로그 파일이 이틀에 걸쳐 있을 수 있습니다. 이 경우 동일한 클러스터에 대해 서로 다른 두 개의 날짜 폴더가 표시됩니다.

* 기본 컨테이너에 로그 파일을 업로드하는 경우, 특히 대형 클러스터의 경우 최대 5분이 걸릴 수 있습니다. 따라서 로그에 액세스하려면 스크립트 동작이 실패하는 경우 클러스터를 즉시 삭제하지 않아야 합니다.

## <a name="ambari-watchdog"></a>Ambari 감시

Linux 기반 HDInsight 클러스터에서 Ambari Watchdog(hdinsightwatchdog)에 대한 암호는 변경하지 마세요. 암호 변경으로 HDInsight 클러스터에서 새 스크립트 작업을 실행 하는 기능이 중단 됩니다.

## <a name="cant-import-name-blobservice"></a>이름 BlobService를 가져올 수 없음

__증상__ . 스크립트 작업이 실패했습니다. Ambari에서 작업을 볼 때 다음 오류와 비슷한 텍스트가 표시됩니다.

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__원인__ . 이 오류는 HDInsight 클러스터에 포함된 Python Azure Storage 클라이언트를 업그레이드하는 경우에 발생합니다. HDInsight는 Azure Storage 클라이언트 0.20.0을 예상합니다.

__해결__ . 이 오류를 해결하려면 `ssh`를 사용하여 각 클러스터 노드에 수동으로 연결합니다. 다음 명령을 실행하여 올바른 스토리지 클라이언트 버전을 다시 설치합니다.

```bash
sudo pip install azure-storage==0.20.0
```

SSH를 사용하여 클러스터에 연결하는 방법에 대한 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>클러스터를 만드는 동안 스크립트가 기록에 표시되지 않음

2016년 3월 15일 이전에 클러스터를 만든 경우에는 스크립트 동작 기록에 항목이 표시되지 않을 수 있습니다. 클러스터의 크기를 조정하면 스크립트가 스크립트 동작 기록에 표시됩니다.

두 가지 예외 사항이 있습니다.

* 클러스터가 2015년 9월 1일 이전에 만들어졌습니다. 스크립트 동작이 이 날에 도입되었습니다. 이 날짜 이전에 만든 클러스터는 클러스터를 만드는 데 스크립트 동작을 사용할 수 없었습니다.

* 클러스터를 만드는 동안 여러 스크립트 동작을 사용했습니다. 또는 여러 스크립트에 대해 동일한 이름을 사용했거나, 여러 스크립트에 대해 동일한 이름과 URI를 사용했지만 서로 다른 매개 변수를 사용했습니다. 이러한 경우 발생하는 오류는 다음과 같습니다.

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.