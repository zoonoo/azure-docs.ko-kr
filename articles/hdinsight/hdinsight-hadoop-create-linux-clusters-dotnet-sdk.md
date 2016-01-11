<properties
   	pageTitle="HDInsight .NET SDK를 사용하여 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터 만들기 | Microsoft Azure"
   	description="HDInsight .NET SDK를 사용하여 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터를 만드는 방법을 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="jgao"/>

#.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 문서에서는 .NET SDK를 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법을 보여 줍니다.

> [AZURE.IMPORTANT]이 문서의 단계에서는 하나의 작업자 노드가 있는 클러스터를 만듭니다. 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

##필수 조건

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __Visual Studio 2013 또는 2015__

##콘솔 응용 프로그램 만들기

1. Visual Studio 2013 또는 2015 열기

2. 다음 설정으로 새 Visual Studio 프로젝트를 만듭니다.

    |속성|값|
    |--------|-----|
    |Template|Templates/Visual C#/Windows/Console Application|
    |이름|CreateHDICluster|

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    .NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.

6. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 열고 다음 코드를 붙여 넣은 후 변수의 값을 제공합니다.

        using System;
        using System.Security;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        namespace CreateHDICluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                
                //Replace SUBSCRIPTIONID with your Azure subscription ID
                private static Guid SubscriptionId = new Guid(SUBSCRIPTIONID);
                
                //Replace GROUPNAME with the name of the resource group to create the cluster in
                private const string ResourceGroupName = "GROUPNAME";
                
                //Replace CLUSTERNAME with the name of the HDInsight cluster you wish to create
                private const string NewClusterName = "CLUSTERNAME";
                private const int NewClusterNumNodes = 1;
                
                //Replace LOCATION with the geographic region that the resource group, storage account, and HDInsight cluster are in
                private const string NewClusterLocation = "LOCATION";
                private const string NewClusterVersion = "3.2";
                
                //Replace STORAGENAME with the name of the storage account to use
                private const string ExistingStorageName = "STORAGENAME.blob.core.windows.net";
                
                //Replace STORAGEKEY with the key for the storage account
                private const string ExistingStorageKey = "STORAGEKEY";
                
                //Replace CONTAINERNAME with the container to use for HDInsight's default storage
                private const string ExistingContainer = "CONTAINTERNAME";
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
            private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterUsername = "admin";
                
                //Replace ADMINPASSWORD with the password for the admin account
                private const string NewClusterPassword = "ADMINPASSWORD";
                
                //Replace SSHUSER with the user name you want to use with logging in to the cluster through SSH
                private const string NewClusterSshUserName = "SSHUSER";
                
                //Replace SSHPUBLICKEY with the public key certificate to use when authenticating the SSH user. For more information on generating and using SSH keys with HDInsight, see https://azure.microsoft.com/ko-KR/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/ and https://azure.microsoft.com/ko-KR/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/
                private const string NewClusterSshPublicKey = @"SSHPUBLICKEY";
        
                private static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");
                    
                    //Authenticate to your subscription
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    //Get an HDIManagement client
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    //Create a new cluster
                    CreateCluster();
                }
        
                public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
        
        
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        Location = NewClusterLocation,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,
        
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingContainer,
        
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
        
                    ScriptAction rScriptAction = new ScriptAction("Install R",
                        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");
        
                    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
                parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
                    
                    _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
                }
        
            }
        }

		
10. 클래스 멤버 값을 대체합니다.

7. **F5** 키를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 또한 Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. HDInsight 클러스터를 만들려면 몇 분정도 걸릴 수 있습니다. 일반적으로 15분 정도입니다.

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux)

###Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

###Spark 클러스터

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_1223_2015-->