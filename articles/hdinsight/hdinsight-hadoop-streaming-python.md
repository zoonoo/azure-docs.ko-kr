---
title: "HDInsight에서 Python MapReduce 작업 개발 | Microsoft Aure"
description: "Linux 기반 HDInsight 클러스터에서 Python MapReduce 작업을 만들고 실행하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 71acfdc7748b85b64d4c46072d5c8ee61c0b1768
ms.lasthandoff: 04/12/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>HDInsight용 Python 스트리밍 프로그램 개발

Hadoop은 MapReduce용 스트리밍 API를 제공합니다. 이 API를 사용하여 Java 이외의 언어로 map 및 reduce 함수를 작성할 수 있습니다. 이 문서에서는 MapReduce 작업을 수행하기 위해 Python을 사용하는 방법을 배웁니다.

이 문서는 [Python으로 Hadoop MapReduce 프로그램 작성](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)에서 Michael Noll이 게시한 정보 및 예제를 기반으로 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Linux 기반 Hadoop

  > [!IMPORTANT]
  > 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)을 참조하세요.

* 텍스트 편집기

  > [!IMPORTANT]
  > 텍스트 편집기에서 줄 끝으로 LF를 사용해야 합니다. CRLF를 사용하는 경우 Linux 기반 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 오류가 발생할 수 있습니다. 확실하지 않은 경우 [MapReduce 실행](#run-mapreduce) 섹션의 선택 단계를 사용하여 모든 CRLF를 LF로 변환합니다.

* **SSH 및 SCP 사용 경험**. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="word-count"></a>단어 개수

이 예제에서는 매퍼와 리듀서를 사용하여 기본 단어 수 계산을 구현합니다. mapper는 문장을 개별 단어로 끊고 reducer는 출력하기 위해 단어와 개수를 집계합니다.

다음 순서도는 map 및 reduce 단계가 어떻게 진행되는지 보여 줍니다.

![map reduce 그림](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Python을 사용하는 이유

Python은 범용, 상위 수준 프로그래밍 언어이며 다른 언어에 비해 더 적은 줄의 코드로 개념을 표현할 수 있습니다. 데이터 과학자들로부터 인터프린트가 자연스러우며 동적으로 입력할 수 있고, 세련된 구문을 가지고 있어서 빠른 응용 프로그램 개발에 적합한 프로토타입 언어로 인기를 얻고 있습니다.

Python은 모든 HDInsight 클러스터에 설치됩니다.

## <a name="streaming-mapreduce"></a>MapReduce 스트리밍

Hadoop을 사용하면 작업에서 사용되는 map 및 reduce 논리를 포함하는 파일을 지정할 수 있습니다. map 및 reduce 논리에 대한 특정 요구 사항은 다음과 같습니다.

* **입력**: map 및 reduce 구성 요소는 STDIN에서 입력 데이터를 읽어야 합니다.
* **출력**: map 및 reduce 구성 요소는 STDOUT에 출력 데이터를 작성해야 합니다.
* **데이터 형식**: 소비되고 생성되는 데이터는 탭 문자로 구분하는 키/값 쌍이어야 합니다

Python은 STDIN에서 읽을 수 있는 **sys** 모듈 및 STDOUT에 출력하는 **print**를 사용하여 이러한 요구 사항을 쉽게 처리할 수 있습니다. 나머지 작업은 키와 값 사이에 탭(`\t`) 문자를 사용하여 데이터 서식을 지정하기만 하면 됩니다.

## <a name="create-the-mapper-and-reducer"></a>mapper 및 reducer 만들기

mapper 및 reducer는 텍스트 파일이며 이 예제에서는 어떤 것이 어떤 작업을 수행하는지 명확하게 하기 위해 **mapper.py** 및 **reducer.py**를 사용합니다. 선택한 편집기를 사용하여 mapper와 reducer를 만들 수 있습니다.

### <a name="mapperpy"></a>Mapper.py

**mapper.py** 라는 새 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

코드를 읽어 보면 이 코드의 내용을 이해할 수 있습니다.

### <a name="reducerpy"></a>reducer.py

**reducer.py** 라는 새 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>파일 업로드

**mapper.py** 및 **reducer.py** 모두 실행하기 전에 클러스터의 헤드 노드에 있어야 합니다. 이 섹션에서는 예제 `scp` 명령 및 파일을 클러스터에 업로드하는 데 사용할 수 있는 Azure PowerShell 스크립트를 제공합니다.

> [!IMPORTANT]
> `scp`와 PowerShell을 사용하여 파일을 업로드하는 데 있어 중요한 차이점은 다음과 같습니다.
>
> * `scp`를 사용하면 파일을 클러스터의 기본 헤드 노드에 저장합니다. 여기서는 나중에 헤드 노드에 연결하여 SSH 세션에서 작업을 실행한다고 가정합니다.
> * PowerShell 스크립트를 사용하면 파일을 클러스터의 기본 저장소에 저장합니다. 여기서는 나중에 PowerShell 스크립트를 사용하여 원격 클라이언트에서 작업을 실행한다고 가정합니다.

### <a name="upload-using-scp"></a>scp를 사용하여 업로드

개발 환경의 **mapper.py** 및 **reducer.py**와 같은 디렉터리에서 다음 명령을 사용합니다. **username**은 클러스터의 SSH 사용자 이름으로, **clustername**은 클러스터 이름으로 바꿉니다.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

로컬 시스템에서 헤드 노드로 파일이 복사됩니다.

> [!NOTE]
> SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다(예: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`).

### <a name="upload-using-powershell"></a>PowerShell을 사용하여 업로드

1. 개발 환경에서 `Put-FilesInHDInsight.ps1`이라는 이름의 새 파일을 만들고 파일의 내용으로 다음을 사용합니다.

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. 이 스크립트를 사용하여 파일을 업로드하려면 Azure PowerShell 프롬프트에서 다음을 사용합니다.

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    메시지가 표시되면 클러스터의 HTTPS 로그인 자격 증명을 입력합니다.

3. 명령을 반복하여 `mapper.py`를 `reducer.py`로 바꿉니다. 이렇게 하면 매퍼 및 리듀서 파일을 모두 클러스터의 기본 저장소에 업로드합니다.

## <a name="run-mapreduce-ssh"></a>MapReduce 실행(SSH)

다음 단계에 따라 클러스터에 연결하고 SSH 세션에서 스트리밍 MapReduce 작업을 실행합니다.

1. SSH를 사용하여 클러스터에 연결합니다.

   `ssh username@clustername-ssh.azurehdinsight.net`

   > [!NOTE]
   > SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다(예: `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`).

2. (선택 사항) mapper.py 및 reducer.py 파일을 만들 때 줄 끝으로 CRLF를 사용하는 텍스트 편집기를 사용하거나 편집기가 사용하는 줄 끝을 모르는 경우 다음 명령을 사용하여 mapper.py 및 reducer.py에서 CRLF를 LF로 변환합니다.

    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. 다음 명령을 사용하여 MapReduce 작업을 시작합니다.

    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`

    다음은 명령어의 일부입니다.

   * **hadoop-streaming.jar**: 스트리밍 MapReduce 작업을 수행할 때 사용합니다. 제공하는 외부 MapReduce 코드로 Hadoop에 접속합니다.

   * **-files**: 이 MapReduce 작업에 필요한 Hadoop 특정 파일을 지정하고 모든 작업자 노드에 복사됩니다.

   * **-mapper**: Hadoop mapper로 사용될 파일을 지정합니다.

   * **-reducer**: Hadoop reducer로 사용할 파일을 지정합니다.

   * **-input**: 단어 수를 계산할 입력 파일을 지정합니다.

   * **-output**: 출력 내용을 작성할 디렉터리를 지정합니다.

     > [!NOTE]
     > 이 명령어로 디렉터리를 새로 만들 수 있습니다.

작업을 시작하면 많은 **INFO** 문이 표시되며 마지막으로 **map** 및 **reduce** 작업 비율이 표시됩니다.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

작업이 완료되면 작업에 대한 상태 정보가 표시됩니다.

## <a name="run-mapreduce-powershell"></a>MapReduce 실행(PowerShell)

개발 환경에서 다음 단계에 따라 PowerShell에서 스트리밍 MapReduce를 실행합니다. PowerShell 스크립트에서는 WebHCat을 사용하여 HDInsight 클러스터에 연결하여 작업을 실행합니다.

1. `Start-HDInsightStreamingPythonJob`이라는 새 파일을 만들고 파일의 내용으로 다음을 사용합니다.

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Azure PowerShell 프롬프트에서 다음을 사용하여 작업을 실행합니다.

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    클러스터에 업로드된 이전 `mapper.py` 및 `reducer.py` 파일을 사용하여 `davinci.txt` 파일의 단어 수를 계산합니다. 출력은 클러스터의 기본 저장소의 `/example/wordcount` 폴더에 저장됩니다.

    작업이 완료되면 다음과 비슷한 정보가 표시됩니다.

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>출력 보기

작업의 출력은 `/example/wordcountout` 디렉터리에 저장됩니다. 이 출력은 SSH 세션에서 보거나 로컬로 다운로드하여 PowerShell에서 볼 수 있습니다.

SSH 세션의 데이터를 클러스터에서 보려면 다음 명령을 사용합니다.

`hdfs dfs -text /example/wordcountout/part-00000`

그러면 단어 목록과 해당 단어가 나타난 횟수가 표시됩니다. 다음은 출력 데이터의 예제입니다.

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

PowerShell을 사용하여 개발 환경에서 출력을 보려면 다음 단계를 수행합니다.

1. `Get-FilesInHDInsight.ps1`이라는 새 파일을 만들고 파일의 내용으로 다음을 사용합니다.

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Azure PowerShell 프롬프트에서 다음을 사용하여 스크립트를 실행하고 출력을 봅니다.

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    그러면 단어 목록과 해당 단어가 나타난 횟수가 표시됩니다. 다음은 출력 데이터의 예제입니다.

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>다음 단계

HDInsight에서 스트리밍 MapRedcue 작업을 사용하는 방법을 배웠으므로 이제 아래 링크를 사용하여 Azure HDInsight에서 작업하는 다른 방법을 살펴봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

