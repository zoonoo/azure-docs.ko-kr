---
title: "HDInsight 클러스터에서 SSH와 Hadoop Pig 사용 | Microsoft Docs"
description: "SSH를 사용하여 Linux 기반 Hadoop 클러스터에 연결한 다음 Pig 명령을 사용하여 Pig Latin 문을 대화형으로 실행하거나 일괄 처리 작업으로 실행하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: edda959693061850248ba97795c2390a592cbb21
ms.lasthandoff: 04/18/2017


---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Pig 명령(SSH)를 사용하여 Linux 기반 클러스터에서 Pig 작업 실행

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

SSH 연결에서 HDInsight 클러스터로 Pig 작업을 대화형으로 실행하는 방법에 대해 알아보세요. Pig Latin 프로그래밍 언어를 사용하면 원하는 출력을 생성하는 입력 데이터에 적용되는 변환을 설명할 수 있습니다.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux 기반 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)를 참조하세요.

## <a id="ssh"></a>SSH를 사용하여 연결

SSH를 사용하여 HDInsight 클러스터에 연결합니다. 다음은 **sshuser** 계정으로 **myhdinsight** 클러스터에 연결하는 예입니다.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**SSH 인증을 위해 인증서 키를 제공한 경우** HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**SSH 인증을 위해 암호를 제공한 경우** HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 입력합니다.

HDInsight에서의 SSH 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="pig"></a>Pig 명령 사용

1. 연결되면 다음 명령을 사용하여 Pig CLI(명령줄 인터페이스)를 시작합니다.

        pig

    잠시 후 `grunt>` 프롬프트가 표시됩니다.

2. 다음 문을 입력합니다.

        LOGS = LOAD '/example/data/sample.log';

    이 명령은 sample.log 파일의 내용을 로그에 로드합니다. 다음 문을 사용하여 파일의 내용을 볼 수 있습니다.

        DUMP LOGS;

3. 이어서 다음 문을 사용하여 각 레코드에서 로깅 수준만 추출하는 정규식을 적용하여 데이터를 변환합니다.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    **DUMP** 를 사용하여 변환 후 데이터를 볼 수 있습니다. 이 예제의 경우 `DUMP LEVELS;`입니다.

4. 다음 표에서 문을 사용하여 계속해서 변환을 적용합니다.

    | Pig Latin 문 | 문의 기능 |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | 로그 수준의 null 값이 포함된 행을 제거하고 결과를 `FILTEREDLEVELS`에 저장합니다. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | 행을 로그 수준으로 그룹화하고 결과를 `GROUPEDLEVELS`에 저장합니다. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | 고유한 각 로그 수준 값 및 발생 횟수를 포함하는 데이터 집합을 만듭니다. 데이터 집합은 `FREQUENCIES`에 저장됩니다. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | 로그 수준을 개수(내림차순)를 기준으로 정렬하고 `RESULT`에 저장합니다. |

    > [!TIP]
    > `DUMP`를 사용하여 각 단계 후의 변환 결과를 확인합니다.

5. `STORE` 문을 사용하여 변환 결과를 저장할 수도 있습니다. 예를 들어 다음 문은 `RESULT`를 클러스터 기본 저장소의 `/example/data/pigout` 디렉터리에 저장합니다.

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > 데이터는 `part-nnnnn` 파일의 지정된 디렉터리에 저장됩니다. 해당 디렉터리가 이미 존재하는 경우 오류가 발생합니다.

6. 프롬프트를 종료하려면 다음 문을 입력합니다.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin 배치 파일

Pig 명령을 사용하여 파일에 포함된 Pig Latin을 실행할 수도 있습니다.

1. 프롬프트를 종료한 후 다음 명령을 사용하여 STDIN을 `pigbatch.pig` 파일에 연결합니다. 이 파일은 SSH 사용자 계정의 홈 디렉터리에 생성됩니다.

        cat > ~/pigbatch.pig

2. 다음 줄에 입력하거나 붙여 넣은 다음 완료되면 Ctrl+D를 사용합니다.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. 다음의 Pig 명령을 사용하여 `pigbatch.pig` 파일을 실행합니다.

        pig ~/pigbatch.pig

    배치 작업이 완료되면 다음과 같은 출력이 표시됩니다.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>다음 단계

HDInsight의 Pig에 대한 일반적인 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop을 사용하여 작업하는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

