---
title: .NET 및 HDInsight를 사용하여 Apache Sqoop 작업 실행 - Azure
description: HDInsight .NET SDK를 사용하여 아파치 하눕 클러스터와 Azure SQL 데이터베이스 간에 아파치 Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157069"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop에 대해 .NET SDK를 사용하여 Apache Sqoop 작업 실행

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight .NET SDK를 사용하여 HDInsight에서 아파치 Sqoop 작업을 실행하여 HDInsight 클러스터와 Azure SQL Database 또는 SQL Server 데이터베이스 간에 내보내기하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [HDInsight에서 하두롭과 아파치 Sqoop사용에서](./hdinsight-use-sqoop.md) [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 완료 .

* [비주얼 스튜디오](https://visualstudio.microsoft.com/vs/community/).

* Sqoop에 익숙합니다. 자세한 내용은 [Sqoop 사용자 가이드를](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)참조하십시오.

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK를 사용하여 HDInsight 클러스터에서 Sqoop 사용

HDInsight .NET SDK는 .NET 클라이언트 라이브러리를 제공하므로 .NET에서 HDInsight 클러스터 작업을 더 쉽게 수행할 수 있습니다. 이 섹션에서는 C# 콘솔 응용 프로그램을 `hivesampletable` 만들어 필수 구성 프로그램에서 만든 Azure SQL Database 테이블로 내보낼 수 있습니다.

## <a name="set-up"></a>설정

1. Visual Studio를 시작하고 C# 콘솔 응용 프로그램을 만듭니다.

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔로** 이동 하 고 다음 명령을 실행 합니다.

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop 내보내기

하이브에서 SQL 서버까지.  이 예제에서는 Hive `hivesampletable` 테이블의 `mobiledata` 데이터를 SQL 데이터베이스의 테이블로 내보올 수 있습니다.

1. Program.cs 파일에서 다음 코드를 사용합니다. 에 대한 `ExistingClusterName`값을 설정하려면 코드를 `ExistingClusterPassword`편집합니다.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. 프로그램을 실행하려면 **F5** 키를 선택합니다.

## <a name="sqoop-import"></a>Sqoop 가져오기

SQL 서버에서 Azure 저장소로. 이 예제는 수행된 위의 내보내기에 따라 달라집니다.  이 예제에서는 SQL `mobiledata` Database의 테이블에서 `wasb:///tutorials/usesqoop/importeddata` 클러스터의 기본 저장소 계정의 디렉터리로 데이터를 가져옵니다.

1. `//sqoop start //sqoop end` 블록의 위의 코드를 다음 코드로 바꿉니다.

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. 프로그램을 실행하려면 **F5** 키를 선택합니다.

## <a name="limitations"></a>제한 사항

Linux 기반 HDInsight에에 대한 제한 사항은 다음과 같습니다.

* 대량 내보내기: 데이터를 Microsoft SQL Server 또는 Azure SQL Database로 내보내는 데 사용되는 Sqoop 커넥터는 현재 대량 삽입을 지원하지 않습니다.

* 일괄 처리: 스위치를 `-batch` 사용하여 Sqoop은 삽입 작업을 일괄 처리하는 대신 여러 인서트를 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight와 아파치 Oozie를 사용](../hdinsight-use-oozie-linux-mac.md): Oozie 워크플로우에서 Sqoop 작업을 사용합니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight 또는 Azure Blob Storage에 데이터를 업로드하는 다른 방법을 찾습니다.
