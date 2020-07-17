---
title: Azure HDInsight에서 Apache Hadoop에 SCP 사용
description: 이 문서에서는 ssh 및 scp 명령을 사용 하 여 HDInsight에 연결 하는 방법에 대 한 정보를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2f602c23f182ede2b9897563a8421163e1328e24
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079193"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Hadoop에 SCP 사용

이 문서에서는 HDInsight 클러스터와 파일을 안전 하 게 전송 하는 방법을 설명 합니다.

## <a name="copy-files"></a>파일 복사

`scp` 유틸리티를 사용하면 클러스터의 개별 노드로 파일을 복사할 수 있습니다. 예를 들어 다음 명령은 로컬 시스템의 `test.txt` 디렉터리를 기본 헤드 노드로 복사합니다.

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

`:` 다음에 지정된 경로가 없으므로 파일은 `sshuser` 홈 디렉터리에 배치됩니다.

다음 예제에서는 `test.txt` 파일을 기본 헤드 노드의 `sshuser` 홈 디렉터리에서 로컬 시스템으로 복사합니다.

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp`는 클러스터 내 개별 노드의 파일 시스템에만 액세스할 수 있으며 클러스터에 대 한 HDFS 호환 저장소의 데이터에 액세스 하는 데 사용할 수 없습니다.

SSH 세션에서 사용할 리소스를 업로드해야 할 때 `scp`를 사용합니다. 예를 들어 Python 스크립트를 업로드한 다음 SSH 세션에서 스크립트를 실행합니다.

HDFS 호환 스토리지로 데이터 직접 로드에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Storage를 사용하는 HDInsight](hdinsight-hadoop-use-blob-storage.md)
* [Azure Data Lake Storage를 사용하는 HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>다음 단계

* [HDInsight와 함께 SSH 사용](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md#access-an-edge-node)
