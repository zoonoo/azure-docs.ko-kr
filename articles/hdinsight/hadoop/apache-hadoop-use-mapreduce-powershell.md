---
title: Apache Hadoop으로 MapReduce 및 PowerShell 사용 - Azure HDInsight
description: PowerShell을 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업을 원격으로 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 2eb5817a3339494417bb426bfdccb09ae3ac7230
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087778"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>PowerShell을 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업 실행

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 Azure PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 MapReduce 작업을 실행하는 예제를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. [Azure Portal를 사용 하 여 Apache Hadoop 클러스터 만들기를](../hdinsight-hadoop-create-linux-clusters-portal.md)참조 하세요.

* PowerShell [Az 모듈](https://docs.microsoft.com/powershell/azure/overview)이 설치되었습니다.

## <a name="run-a-mapreduce-job"></a>MapReduce 작업 실행

Azure PowerShell은 HDInsight에서 MapReduce 작업을 원격으로 실행할 수 있는 *cmdlet* 을 제공합니다. PowerShell은 내부적으로 HDInsight 클러스터에서 실행되는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (이전의 Templeton)에 REST를 호출합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 사용됩니다.

|Cmdlet | Description |
|---|---|
|연결 AzAccount|Azure 구독에 대해 Azure PowerShell을 인증합니다.|
|AzHDInsightMapReduceJobDefinition|지정한 MapReduce 정보를 사용하여 새 *작업 정의*를 만듭니다.|
|AzHDInsightJob|HDInsight에 작업 정의를 보내고 작업을 시작합니다. *작업* 개체가 반환됩니다.|
|대기 AzHDInsightJob|작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료되거나 대기 시간이 초과될 때까지 기다립니다.|
|AzHDInsightJobOutput|작업의 출력을 검색하는 데 사용합니다.|

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용 하 여 **mapreducejob.ps1**으로 다음 코드를 저장 합니다.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. 새 **Azure PowerShell** 명령 프롬프트를 엽니다. **mapreducejob.ps1** 파일의 디렉터리 위치를 변경한 다음 명령을 사용하여 스크립트를 실행합니다.

        .\mapreducejob.ps1

    스크립트를 실행할 때 HDInsight 클러스터의 이름과 클러스터 로그인을 입력 하 라는 메시지가 표시 됩니다. Azure 구독에서 인증을 받으라는 메시지도 표시될 수 있습니다.

3. 작업이 완료되면 다음 텍스트와 유사한 출력이 나타납니다.

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    이 출력은 작업이 성공적으로 완료되었음을 나타냅니다.

    > [!NOTE]  
    > **ExitCode** 가 0이 아닌 값이면 [문제 해결](#troubleshooting)을 참조하세요.

    이 예제에서도 다운로드한 파일을 스크립트를 실행한 디렉터리의 **output.txt** 파일에 저장합니다.

### <a name="view-output"></a>출력 보기

작업에서 생성한 단어 및 단어 개수를 보려면 텍스트 편집기에서 **output.txt** 파일을 엽니다.

> [!NOTE]  
> MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

## <a name="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않으면 작업에 대한 오류를 봅니다. 이 작업에 대 한 오류 정보를 보려면 다음 명령을 **mapreducejob.ps1** 파일의 끝에 추가 합니다. 그런 다음 파일을 저장 하 고 스크립트를 다시 실행 합니다.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

이 cmdlet은 작업이 실행될 때 STDERR에 작성된 정보를 반환합니다.

## <a name="next-steps"></a>다음 단계

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 MapReduce 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다. HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
