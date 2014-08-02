<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight에서 Hadoop .NET SDK 사용
==================================

Hadoop .NET SDK는 .NET에서 Hadoop으로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 자습서에서는 Hadoop .NET SDK를 가져오는 방법과 Azure HDInsight Service를 사용하여 Hive 쿼리를 실행하는 간단한 .NET 기반 응용 프로그램을 빌드하는 데 해당 SDK를 사용하는 방법에 대해 알아봅니다. 그리고 actors.txt 파일을 사용하여 상을 가장 많이 받은 배우/여배우를 찾는 응용 프로그램을 작성해 봅니다.

HDInsight를 사용하도록 설정하려면 [여기](https://account.windowsazure.com/PreviewFeatures)를 클릭하십시오.

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [Hadoop .NET SDK 다운로드 및 설치](#install)
-   [자습서 준비](#prepare)
-   [응용 프로그램 만들기](#create)
-   [응용 프로그램 실행](#run)
-   [다음 단계](#nextsteps)

Hadoop .NET SDK 다운로드 및 설치
--------------------------------

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)(영문)에서 가장 최근에 게시된 SDK 빌드를 설치할 수 있습니다. SDK에는 다음 구성 요소가 포함되어 있습니다.

-   **MapReduce 라이브러리** - Hadoop 스트리밍 인터페이스를 사용하여 .NET 언어로 MapReduce 작업 작성을 간소화할 수 있습니다.

-   **LINQ to Hive 클라이언트 라이브러리** - C\# 또는 F\# LINQ 쿼리를 HiveQL 쿼리로 변환하여 Hadoop 클러스터에서 실행할 수 있습니다. 또한 이 라이브러리는 .NET 응용 프로그램에서 임의의 HiveQL 쿼리를 실행할 수도 있습니다.

-   **WebClient 라이브러리** - *WebHDFS* 및 *WebHCat*용 클라이언트 라이브러리가 포함되어 있습니다.

    -   **WebHDFS 클라이언트 라이브러리** - HDFS 및 Azure 블로그 저장소의 파일에 대해 작업합니다.

    -   **WebHCat 클라이언트 라이브러리** - HDInsight 클러스터에서 작업의 예약 및 실행을 관리합니다.

라이브러리를 설치하는 NuGet 구문은 다음과 같습니다.

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

이러한 명령은 라이브러리 및 참조를 현재 Visual Studio 프로젝트에 추가합니다.

자습서 준비
-----------

자습서를 진행하려면 먼저 [Azure 구독](http://www.windowsazure.com/en-us/pricing/free-trial/) 및 [Azure 저장소 계정](http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/)이 있어야 합니다. 또한 Azure 저장소 계정 이름 및 계정 키도 알아야 합니다. 이러한 정보를 얻는 방법에 대한 자세한 내용은 *방법: 저장소 액세스 키 보기, 복사 및 다시 생성* 섹션([저장소 계정을 관리하는 방법](/en-us/manage/services/storage/how-to-manage-a-storage-account/))을 참조하십시오.

또한 이 자습서에서 사용할 Actors.txt 파일도 다운로드해야 합니다. 다음 단계를 수행하여 이 파일을 개발 환경에 다운로드합니다.

1.  로컬 컴퓨터에 C:\\Tutorials 폴더를 만듭니다.

2.  [Actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003)(영문)를 다운로드하여 C:\\Tutorials 폴더에 저장합니다.

응용 프로그램 만들기
--------------------

이 섹션에서는 Hadoop 클러스터에 프로그래밍 방식으로 파일을 업로드하는 방법 및 LINQ to Hive를 사용하여 Hive 작업을 실행하는 방법에 대해 알아봅니다.

1.  Visual Studio 2012를 엽니다.

2.  파일 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.

3.  새 프로젝트에서 다음 값을 입력하거나 선택합니다.

    <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
 <tr data-morhtml="true">
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
 </table>


4.  **확인**을 클릭하여 프로젝트를 만듭니다.

5.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6.  콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

         install-package Microsoft.Hadoop.Hive 
         install-package Microsoft.Hadoop.WebClient 

    .NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.

7.  솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8.  파일 맨 위에 다음 using 문을 추가합니다.

         using Microsoft.Hadoop.WebHDFS.Adapters;
         using Microsoft.Hadoop.WebHDFS;
         using Microsoft.Hadoop.Hive;

9.  Main() 함수에서 다음 코드를 복사하여 붙여넣습니다.

         // Upload actors.txt to Blob Storage
         var asvAccount = [Storage-account-name.blob.core.windows.net];
         var asvKey = [Storage account key];
         var asvContainer = [Container name];
         var localFile = "C:/Tutorials/Actors.txt";
         var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
         var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
         var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
            
         var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
         var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
            
         Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
         HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
         HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
            
         // Create Hive connection
         var hiveConnection = new HiveConnection(
           new System.Uri(clusterURI),
           hadoopUser, 
           hadoopUserPassword,
           asvAccount, 
           asvKey);
            
         // Drop any existing tables called Actors
         // Only needed if you wish to rerun this application
         // and drop a previous Actors table.
         //hiveConnection.GetTable<HiveRow>("Actors").Drop();

         Console.WriteLine("Creating a Hive table named 'Actors'...");
         string command =
           @"CREATE TABLE Actors(
                     MovieId string, 
                     ActorId string,
                     Name string, 
                     AwardsCount int) 
                     row format delimited fields 
                 terminated by ',';";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
         command =
           @"LOAD DATA INPATH 
                 '/user/hadoop/MovieData/Actors.txt'
             OVERWRITE INTO TABLE Actors;";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Performing Hive query...");
         var result = hiveConnection.ExecuteQuery(@"select name, awardscount
                   from actors sort by awardscount desc
                   limit 1");
         result.Wait();

         Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
         Console.WriteLine("\nPress any key to continue.");
         Console.ReadKey();

10. 응용 프로그램에서 상수를 업데이트합니다. Azure HDInsight Service는 Azure Blob 저장소를 기본 파일 시스템으로 사용합니다. HDInsight 프로비전 프로세스 중 Blob이 기본 파일 시스템으로 지정됩니다. 기본 파일 시스템 컨테이너를 사용하거나 다른 Blob 저장소의 컨테이너를 사용하는 옵션이 제공됩니다. 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소 사용](/en-us/manage/services/hdinsight/howto-blob-store/)을 참조하십시오.

    기본 파일 시스템 컨테이너를 사용하도록 선택한 경우 클러스터에 원격으로 액세스하여 *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml* 구성 파일에서 저장소 계정 이름, 저장소 키 및 컨테이너 이름을 검색할 수 있습니다. 기본 파일 시스템으로 사용된 컨테이너는 *fs.default.name*을 검색하여 찾을 수 있으며, 저장소 계정 이름 및 계정 키는 *fs.azure.account.key*를 검색하여 찾을 수 있습니다.

응용 프로그램 실행
------------------

Visual Studio에 응용 프로그램이 열려 있을 때 **F5**를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리며, 데이터가 업로드되고 Hive 테이블에 저장되며 마지막으로 쿼리될 때 응용 프로그램에서 실행한 단계가 표시됩니다. 응용 프로그램이 완료되고 쿼리 결과가 반환되면 아무 키나 눌러 응용 프로그램을 종료합니다.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "콘솔 응용 프로그램")

**참고**

응용 프로그램에서 수행한 각 단계는 완료되는 데 몇 초에서 몇 분 정도 걸릴 수 있습니다. 다른 단계는 클러스터 크기의 영향을 받지만 Actors.txt 파일을 업로드하는 시간은 Azure 데이터 센터에 대한 인터넷 연결에 따라 다릅니다.

**참고**

LOAD DATA INPATH 작업은 Actors.txt 데이터를 Hive 제어 파일 시스템 네임스페이스로 이동하는 이동 작업입니다. 이 작업은 업로드 위치에서 Actors.txt 파일을 효과적으로 제거합니다. 이 때문에 이 응용 프로그램을 실행할 때마다 Actors.txt 파일을 업로드해야 합니다.

데이터를 Hive로 로드하는 방법에 대한 자세한 내용은 [Hive 시작](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations)(영문)을 참조하십시오.

다음 단계
---------

이제 Hadoop .NET SDK를 사용하여 .NET 응용 프로그램을 만드는 방법을 이해할 수 있습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [Azure HDInsight 시작](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [HDInsight와 함께 Pig 사용](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [HDInsight와 함께 MapReduce 사용](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [HDInsight와 함께 Hive 사용](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

