---
title: HDInsight의 Hadoop에서 MapReduce와 함께 C# 사용 - Azure
description: Azure HDInsight에서 Apache Hadoop으로 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561795"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight의 Apache Hadoop에서 MapReduce 스트리밍으로 C# 사용

HDInsight에서 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아보세요.

Apache Hadoop 스트리밍은 스크립트 또는 실행 파일을 사용하여 MapReduce 작업을 실행할 수 있는 유틸리티입니다. 이 예제에서는 단어 수 세기 솔루션을 위해 .NET을 사용하여 매퍼와 리듀서를 구현합니다.

## <a name="net-on-hdinsight"></a>HDInsight에서.NET

HDInsight 클러스터는 [Mono(.NEThttps://mono-project.com) ](https://mono-project.com) 응용 프로그램을 실행하기 위해)를 사용합니다. Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다. HDInsight에 포함된 모노 버전에 대한 자세한 내용은 [다른 HDInsight 버전에서 사용할 수 있는 아파치 하두롭 구성 요소를](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)참조하십시오.

.NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

## <a name="how-hadoop-streaming-works"></a>Hadoop 스트리밍 작동 방식

이 문서의 스트리밍에 사용된 기본 프로세스는 다음과 같습니다.

1. Hadoop은 STDIN의 매퍼(이 예제의*mapper.exe)에* 데이터를 전달합니다.
2. 매퍼가 데이터를 처리하고 탭으로 구분된 키/값 쌍을 STDOUT으로 내보냅니다.
3. 출력은 Hadoop에 의해 읽혀지고 STDIN의 감속기(이 예제에서는*reducer.exe)로* 전달됩니다.
4. 리듀서는 탭으로 구분된 키/값 쌍을 읽고 데이터를 처리한 다음 STDOUT에서 탭으로 구분된 키/값 쌍의 결과를 내보냅니다.
5. Hadoop에서 이 출력을 읽습니다. 그런 다음 출력 디렉터리에 기록됩니다.

스트리밍에 대한 자세한 내용은 [Hadoop 스트리밍](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio.

* .NET Framework 4.5를 대상으로 하는 C# 코드 작성 및 빌드에 대해 잘 알고 있어야 합니다.

* 클러스터로 .exe 파일을 업로드하는 방법. 이 문서의 단계는 Data Lake Tools for Visual Studio를 사용하여 클러스터의 기본 스토리지로 파일을 업로드합니다.

* PowerShell을 사용하는 경우 Az [모듈이](https://docs.microsoft.com/powershell/azure/overview)필요합니다.

* SSH 클라이언트(선택 사항)입니다. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* HDInsight의 Apache Hadoop 클러스터. [리눅스에서 HDInsight로 시작하기를](../hadoop/apache-hadoop-linux-tutorial-get-started.md)참조하십시오.

* 클러스터 기본 스토리지에 대한 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)입니다. Azure Storage는 `wasb://`, Azure Data Lake Storage Gen2는 `abfs://`, Azure Data Lake Storage Gen1은 `adl://`입니다. Azure Storage 또는 Data Lake Storage Gen2에 보안 전송을 사용하는 경우 URI는 각각 `wasbs://` 또는 `abfss://`가 됩니다. [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.


## <a name="create-the-mapper"></a>매퍼 만들기

Visual Studio에서 *매퍼라는*새 .NET Framework 콘솔 응용 프로그램을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

응용 프로그램을 만든 후 빌드하여 프로젝트 디렉터리에서 */bin/Debug/mapper.exe* 파일을 생성합니다.

## <a name="create-the-reducer"></a>리듀서 만들기

Visual Studio에서 *reducer라는*새 .NET Framework 콘솔 응용 프로그램을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

응용 프로그램을 만든 후 프로젝트 디렉터리에서 */bin/Debug/reducer.exe* 파일을 생성하도록 빌드합니다.

## <a name="upload-to-storage"></a>스토리지에 업로드

다음으로, 당신은 HDInsight 스토리지에 *매퍼* 및 *감속기* 응용 프로그램을 업로드해야합니다.

1. 시각적 스튜디오에서**서버 탐색기** **보기를** > 선택합니다.

1. **Azure를**마우스 오른쪽 단추로 클릭하고 **Microsoft Azure 구독에 연결/를**선택하고 로그인 프로세스를 완료합니다.

1. 이 애플리케이션을 배포하려는 HDInsight 클러스터를 확장합니다. 텍스트가 포함된 항목 **(기본 Storage 계정)** 이 목록에 표시됩니다.

   ![스토리지 계정, HDInsight 클러스터, 서버 탐색기, 비주얼 스튜디오](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * **(기본 저장소 계정)** 항목을 확장할 수 있는 경우 **Azure 저장소 계정을** 클러스터의 기본 저장소로 사용하고 있습니다. 클러스터의 기본 저장소에서 파일을 보려면 항목을 확장한 다음 **(기본 컨테이너)를**두 번 클릭합니다.

   * **(기본 저장소 계정)** 항목을 확장할 수 없는 경우 **Azure Data Lake 저장소를** 클러스터의 기본 저장소로 사용하고 있습니다. 클러스터의 기본 스토리지에 있는 파일을 보려면 항목을 확장한 다음 **(기본 Storage 계정)** 을 두 번 클릭합니다.

1. .exe 파일을 업로드하려면 다음 방법 중 하나를 사용합니다.

    * **Azure 저장소 계정을**사용하는 경우 **Blob 업로드** 아이콘을 선택합니다.

        ![매퍼, 비주얼 스튜디오에 대한 HDInsight 업로드 아이콘](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        새 **파일 업로드** 대화 상자에서 **파일 이름**아래에서 **찾아보기를 선택합니다.** **Blob 업로드** 대화 상자에서 *매퍼* 프로젝트의 *bin\debug* 폴더로 이동한 다음 *mapper.exe* 파일을 선택합니다. 마지막으로 **열기를** 선택한 다음 **확인을** 선택하여 업로드를 완료합니다.

    * **Azure Data Lake Storage의**경우 파일 목록에서 빈 영역을 마우스 오른쪽 단추로 클릭한 다음 **업로드를**선택합니다. 마지막으로 *mapper.exe* 파일을 선택한 다음 **열기를**선택합니다.

    *mapper.exe* 업로드가 완료되면 *reducer.exe* 파일의 업로드 프로세스를 반복합니다.

## <a name="run-a-job-using-an-ssh-session"></a>작업 실행: SSH 세션 사용

다음 절차에서 SSH 세션을 사용하여 MapReduce 작업을 실행하는 방법을 설명합니다.

1. [ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령 중 하나를 사용하여 MapReduce 작업을 시작합니다.

   * 기본 저장소가 **Azure 저장소인**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * 기본 저장소가 **데이터 레이크 저장소 Gen1인**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * 기본 저장소가 **데이터 레이크 저장소 Gen2인**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   다음 목록에서는 각 매개 변수 및 옵션이 나타내는 내용을 설명합니다.

   * *hadoop-streaming.jar*: 스트리밍 MapReduce 기능이 포함된 jar 파일을 지정합니다.
   * `-files`: 이 작업에 대한 *mapper.exe* 및 *reducer.exe* 파일을 지정합니다. 각 `wasbs:///` `adl:///`파일 `abfs:///` 앞에 있는 의 는 의 건 반망의 루트에 대한 경로입니다.
   * `-mapper`: 매퍼를 구현하는 파일을 지정합니다.
   * `-reducer`: 감속기 구현 파일을 지정합니다.
   * `-input`: 입력 데이터를 지정합니다.
   * `-output`: 출력 디렉토리를 지정합니다.

3. MapReduce 작업이 완료되면 다음 명령을 사용하여 결과를 봅니다.

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   다음 텍스트는 이 명령에서 반환된 데이터의 예입니다.

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>작업 실행: PowerShell 사용

다음 PowerShell 스크립트를 사용하여 MapReduce 작업을 실행하고 결과를 다운로드합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

이 스크립트는 클러스터 로그인 계정 이름과 암호와 HDInsight 클러스터 이름을 묻습니다. 작업이 완료되면 출력이 *output.txt*라는 파일에 다운로드됩니다. 다음 텍스트는 `output.txt` 파일의 데이터 예제입니다.

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>다음 단계

HDInsight를 사용하여 MapReduce를 사용하는 방법에 대한 자세한 내용은 [HDInsight의 아파치 하두프에서 MapReduce 사용을](hdinsight-use-mapreduce.md)참조하십시오.

Hive 및 Pig에서 C#을 사용하는 방법에 대한 자세한 내용은 [Apache Hive 및 Apache Pig에서 C# 사용자 정의 함수 사용](apache-hadoop-hive-pig-udf-dotnet-csharp.md)을 참조하세요.

HDInsight의 Storm에서 C# 사용에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](../storm/apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.
