---
title: Apache Hadoop와 MapReduce 및 SSH 연결-Azure HDInsight
description: SSH를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업을 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 2736d0cfe514252e36ba6d7e0d71efe09da15aae
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076303"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>SSH를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 사용

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

SSH(Secure Shell) 연결에서 HDInsight로 MapReduce 작업을 제출하는 방법을 알아봅니다.

> [!NOTE]
> Linux 기반 Apache Hadoop 서버 사용에 익숙하지만 HDInsight는 생소하다면 [Linux 기반 HDInsight 팁](../hdinsight-hadoop-linux-information.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

HDInsight의 Apache Hadoop 클러스터. [Azure Portal를 사용 하 여 Apache Hadoop 클러스터 만들기를](../hdinsight-hadoop-create-linux-clusters-portal.md)참조 하세요.

## <a name="use-hadoop-commands"></a>Hadoop 명령 사용

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. HDInsight 클러스터에 연결되면 다음 명령을 사용하여 MapReduce 작업을 시작합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    이 명령은 `hadoop-mapreduce-examples.jar` 파일에 포함되어 있는 `wordcount` 클래스를 시작합니다. 입력으로 `/example/data/gutenberg/davinci.txt` 문서를 사용하고 출력은 `/example/data/WordCountOutput`에 저장됩니다.

    > [!NOTE]
    > 이 MapReduce 작업 및 예 데이터에 대한 자세한 내용은 [HDInsight의 Apache Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

    작업이 처리되는 동안 세부 정보를 내보내며 작업이 완료될 때 다음 텍스트와 유사한 정보를 반환합니다.

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. 작업이 완료되면 다음 명령을 사용하여 출력 파일을 나열합니다.

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    이 명령은 `_SUCCESS` 및 `part-r-00000`의 두 개의 파일을 표시합니다. `part-r-00000` 파일은 이 작업에 대한 출력을 포함합니다.

    > [!NOTE]  
    > 일부 MapReduce 작업은 여러 **part-r-#####** 파일로 결과를 분할할 수 있습니다. 그럴 경우 ##### 접미사가 파일의 순서를 나타냅니다.

1. 출력을 보려면 다음 명령을 사용합니다.

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    이 명령은 **wasbs://example/data/gutenberg/davinci.txt** 파일에 포함된 단어의 목록과 각 단어가 나타나는 횟수를 표시합니다. 다음 텍스트는 파일에 포함된 데이터의 예입니다.

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>다음 단계

여기에서 볼 수 있듯이 Hadoop 명령은 HDInsight 클러스터에서 MapReduce 작업을 실행하고 작업 출력을 볼 수 있는 쉬운 방법을 제공합니다. HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
