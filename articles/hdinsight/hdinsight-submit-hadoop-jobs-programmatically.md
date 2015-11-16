<properties
	pageTitle="HDInsight에서 Hadoop 작업 제출 | Microsoft Azure"
	description="Azure HDInsight Hadoop에 Hadoop 작업을 제출하는 방법에 대해 알아봅니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="jgao"/>

# HDInsight에서 Hadoop 작업 제출

Azure PowerShell을 사용하여 MapReduce, Hive 작업을 제출하는 방법과 HDInsight.NET SDK를 사용하여 MapReduce, Hadoop 스트리밍 및 Hive 작업을 제출하는 방법을 알아봅니다.

> [AZURE.NOTE]이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. HDInsight에서 MapReduce, Hive 또는 Pig 와 작업하도록 Linux, OS X 또는 Unix 클라이언트 사용에 대한 자세한 내용은 다음 문서를 참조하고 각 **SSH** 또는 **Curl** 링크를 선택합니다.
>
> - [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
> - [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
> - [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure HDInsight 클러스터**. 자세한 내용은 [HDInsight 시작][hdinsight-get-started] 또는 [HDInsight에서 Hadoop 클러스터 프로비전][hdinsight-provision]을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.

##PowerShell을 사용하여 MapReduce 작업 제출

[Windows 기반 HDInsight에서 Hadoop MapReduce 샘플 실행](hdinsight-run-samples.md)을 참조하세요.

##PowerShell을 사용하여 Hive 작업 제출

[PowerShell을 사용하여 Hive 쿼리 실행](hdinsight-hadoop-use-hive-powershell.md)을 참조하세요.

## Visual Studio를 사용하여 Hive 작업 제출

[Visual Studio용 HDInsight Hadoop 도구 사용 시작][hdinsight-visual-studio-tools]을 참조하세요.

##PowerShell을 사용하여 Sqoop 작업 제출

[HDInsight와 함께 Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

##HDInsight .NET SDK를 사용하여 Hive/Pig/Sqoop 작업 제출
HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

**작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. 다음 코드를 사용합니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
				private static HDInsightJobManagementClient _hdiJobManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
				private const string ResourceGroupName = "<Your Resource Group Name>";
		
				private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
				private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
				private const string ExistingClusterUsername = "admin";
				private const string ExistingClusterPassword = "**********";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
					var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
					_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
					SubmitHiveJob();
					SubmitPigJob();
					SubmitSqoopJob();
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
				}
		
				private static void SubmitPigJob()
				{
					var parameters = new PigJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Query = @"LOGS = LOAD 'wasb:///example/data/sample.log';
							LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
							FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
							GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
							FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
							RESULT = order FREQUENCIES by COUNT desc;
							DUMP RESULT;"
					};
		
					System.Console.WriteLine("Submitting the Pig job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
				}
		
				private static void SubmitHiveJob()
				{
					Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
					List<string> args = new List<string> { { "argA" }, { "argB" } };
					var parameters = new HiveJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Query = "SHOW TABLES",
						Defines = ConvertDefinesToString(defines),
						Arguments = ConvertArgsToString(args)
					};
		
					System.Console.WriteLine("Submitting the Hive job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
				}
		
				private static void SubmitSqoopJob()
				{
					var sqlDatabaseServerName = "<SQLDatabaseServerName>";
					var sqlDatabaseLogin = "<SQLDatabaseLogin>";
					var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
					var sqlDatabaseDatabaseName = "hdisqoop";
		
					var tableName = "log4jlogs";
					var exportDir = "/tutorials/usesqoop/data";
		
					// Connection string for using Azure SQL Database.
					// Comment if using SQL Server
					var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
					// Connection string for using SQL Server.
					// Uncomment if using SQL Server
					//var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		
					var parameters = new SqoopJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
					};
		
					System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
				}
		
				private static string ConvertDefinesToString(Dictionary<string, string> defines)
				{
					if (defines.Count == 0)
					{
						return null;
					}
		
					return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
				}
				private static string ConvertArgsToString(List<string> args)
				{
					if (args.Count == 0)
					{
						return null;
					}
		
					return string.Join("&arg=", args.ToArray());
				}
			}
		}

5. **F5** 키를 눌러 응용 프로그램을 실행합니다.

##Visual Studio용 HDInsight 도구를 사용하여 작업 제출

Visual Studio용 HDInsight 도구를 사용하여 Hive 쿼리 및 Pig 스크립트를 실행할 수 있습니다. [HDInsight용 Visual Studio Hadoop 도구 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

##다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision]
* [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
* [HDInsight Cmdlet 참조 문서][hdinsight-powershell-reference]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Nov15_HO2-->