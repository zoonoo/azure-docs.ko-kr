---
title: "HDInsight .NET SDK를 사용한 MapReduce 작업 제출 | Microsoft Docs"
description: "HDInsight .NET SDK를 사용하여 Azure HDInsight Hadoop에 MapReduce 작업을 제출하는 방법에 대해 알아봅니다."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 03f3898182d405bced8ad41b281e2ce2037b8343
ms.lasthandoff: 03/04/2017


---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 실행
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

HDInsight .NET SDK를 사용하여 MapReduce 작업을 제출하는 방법을 알아봅니다. HDInsight 클러스터에는 여러 MapReduce 샘플이 담긴 jar 파일이 포함되어 있습니다. 이 jar 파일은 */example/jars/hadoop-mapreduce-examples.jar*입니다.  샘플 중 하나는 *wordcount*입니다. C# 콘솔 응용 프로그램을 개발하여 단어 세기 작업을 제출합니다.  이 작업은 */example/data/gutenberg/davinci.txt* 파일을 읽고 결과를 */example/data/davinciwordcount*에 출력합니다.  응용 프로그램을 다시 실행하려면 출력 폴더를 정리해야 합니다.

> [!NOTE]
> 이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. Hive와 함께 작동하도록 Linux, OS X 또는 Unix 클라이언트를 사용하는 방법에 대한 정보를 보려면 문서 맨 위에 표시된 탭 선택기를 사용합니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **HDInsight에 Hadoop 클러스터**. [HDInsight에서 Linux 기반 Hadoop 사용 시작](hdinsight-use-sqoop.md#create-cluster-and-sql-database)을 참조하세요.
* **Visual Studio 2013/2015/2017**

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK를 사용하여 MapReduce 작업 제출
HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 

**작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. 다음 코드를 사용합니다.
   
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
4. **F5** 키를 눌러 응용 프로그램을 실행합니다.

작업을 다시 실행하려면 작업 출력 폴더 이름을 변경해야 합니다. 이 샘플에서는 "/example/data/davinciwordcount"입니다.

작업이 성공적으로 완료되면 출력은 빈 상태가 됩니다. MapReduce 작업의 결과를 보려면 Azure Portal을 사용하여 Blob Storage에서 기본 저장소 컨테이너를 알아보세요.  파일 이름은 "part-r-00000"입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* Hive 작업 제출에 대해서는 [HDInsight .NET SDK를 사용하여 Hive 쿼리 실행](hdinsight-hadoop-use-hive-dotnet-sdk.md)을 참조하세요.
* HDInsight 클러스터 만들기는 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* HDInsight 클러스터 관리는 [HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md)를 참조하세요.
* HDInsight .NET SDK에 대한 내용은 [HDInsight .NET SDK 참조](https://msdn.microsoft.com/library/mt271028.aspx)를 확인하세요.
* Azure에 대한 비대화형 인증은 [비대화형 인증 .NET HDInsight 응용 프로그램 만들기](hdinsight-create-non-interactive-authentication-dotnet-applications.md)를 참조하세요.


