<properties 
	pageTitle="프로그래밍 방식으로 Hadoop YARN 응용 프로그램 로그에 액세스 | Microsoft Azure" 
	description="HDInsight의 Hadoop 클러스터에서 프로그래밍 방식으로 응용 프로그램 로그에 액세스합니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# 프로그래밍 방식으로 HDInsight의 Hadoop에서 YARN 응용 프로그램 로그에 액세스

이 항목에서는 Azure HDInsight의 Hadoop 클러스터에서 완료된 YARN(Yet Another Resource Negotiator) 응용 프로그램을 프로그래밍 방식으로 열거하는 방식과 원격 데스크톱 프로토콜(RDP)를 사용하여 클러스터에 연결하지 않고 프로그래밍 방식으로 응용 프로그램 로그에 액세스하는 방법에 대해 설명합니다. 특히 다음과 같은 새 구성 요소와 새 API가 추가되었습니다.

  1. HDInsight 클러스터에서 제네릭 응용 프로그램 기록 서버를 사용하도록 설정되었습니다. 이 서버는 YARN Timeline Server 내의 구성 요소로, 완료된 응용 프로그램에 대한 제네릭 정보의 저장 및 검색을 처리합니다.
  2. 클러스터에서 실행한 응용 프로그램을 프로그래밍 방식으로 열거하고 발생하는 응용 프로그램 문제를 디버깅하기 위해 관련 응용 프로그램 또는 컨테이너별 로그(일반 텍스트)를 다운로드하는 데 Azure HDInsight .Net SDK의 API를 사용할 수 있습니다.


## 필수 조건

.NET Framework 응용 프로그램에서 이 항목에서 제공하는 코드를 사용하려면 Azure HDInsight SDK가 필요합니다. 최근 게시된 SDK 빌드는 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서 다운로드할 수 있습니다.

Visual Studio 응용 프로그램에서 HDInsight SDK를 설치하려면 **도구** 메뉴로 이동하고 **Nuget 패키지 관리자**를 클릭한 후 **패키지 관리자 콘솔**을 클릭합니다. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

이 명령은 HDInsight용 .NET 라이브러리와 이 라이브러리에 대한 참조를 현재 Visual Studio 프로젝트에 추가합니다.


## <a name="YARNTimelineServer"></a>YARN Timeline Server

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a>(영문)는 두 가지 다른 인터페이스를 통해 완료된 응용 프로그램에 대한 제네릭 정보 및 프레임워크별 응용 프로그램 정보를 제공합니다. 구체적으로 살펴보면 다음과 같습니다.

* 3.1.1.374 이상 버전에서는 HDInsight 클러스터에서 제네릭 응용 프로그램 정보를 저장하고 검색할 수 있습니다. 
* Timeline Server의 프레임워크별 응용 프로그램 정보 구성 요소는 HDInsight 클러스터에서 현재 사용할 수 없습니다.


응용 프로그램에 대한 제네릭 정보에는 다음과 같은 데이터가 포함됩니다.

* 응용 프로그램의 고유한 식별자인 응용 프로그램 ID 
* 응용 프로그램을 시작한 사용자 
* 응용 프로그램을 완료하려고 시도한 횟수 
* 지정된 응용 프로그램 시도에 사용된 컨테이너 

HDInsight 클러스터에서 이 정보는 Azure 리소스 관리자가 기본 Azure 저장소 계정의 기본 컨테이너에 있는 기록 저장소에 저장합니다. 완료된 응용 프로그램에 대한 이 제네릭 데이터는 REST API를 통해 검색할 수 있습니다.

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Microsoft는 이 데이터를 프로그래밍 방식으로 쉽게 검색할 수 있게 해주는 새 API를 HDInsight .NET SDK에 추가했습니다. 또한 RDP를 사용하는 클러스터에 연결한 후 클러스터 노드에서 직접 YARN 명령줄 인터페이스(CLI) 명령을 실행하여 제네릭 데이터를 검색할 수도 있습니다.

## <a name="YARNAppsAndLogs"></a>YARN 응용 프로그램 및 로그

YARN은 여러 프로그래밍 모델(예: MapReduce)을 지원하여 리소스 관리를 응용 프로그램 예약/모니터링과 분리합니다. 이는 전역 *리소스 관리자*(RM), 작업자 노드별 *노드 관리자*(NM) 및 응용 프로그램별 *응용 프로그램 마스터*(AM)를 통해 이루어집니다. 응용 프로그램별 AM은 응용 프로그램을 실행하기 위한 리소스(CPU, 메모리, 디스크, 네트워크)를 RM과 협상합니다. RM은 NM과 협력하여 이러한 리소스를 부여하며, 이 리소스는 *컨테이너는*로 부여됩니다. AM은 RM에 의해 부여받은 컨테이너의 진행률 추적합니다. 응용 프로그램의 특성에 따라 응용 프로그램에 여러 컨테이너가 필요할 수 있습니다.

또한 각 응용 프로그램은 작동 중단이 발생하거나 AM과 RM 간의 통신 손실로 인해 응용 프로그램을 완료하기 위해 여러 *응용 프로그램 시도*로 구성될 수도 있습니다. 따라서 컨테이너는 응용 프로그램의 특정 시도에 부여됩니다. 컨테이너는 YARN 응용 프로그램에서 수행하는 기본 작업 단위에 대한 컨텍스트를 제공하고 컨테이너의 컨텍스트 내에서 수행되는 모든 작업은 컨테이너가 할당된 단일 작업자 노드에서 수행된다고 볼 수 있습니다. 자세한 내용은 [YARN 개념][YARN-concepts](영문)을 참조하세요.

응용 프로그램 로그(및 연관된 컨테이너 로그)는 문제가 있는 Hadoop 응용 프로그램을 디버깅하는 데 매우 중요합니다. YARN은 [로그 집계][log-aggregation](영문) 기능을 사용하여 응용 프로그램 로그를 수집, 집계 및 저장하기 위한 유용한 프레임워크를 제공합니다. 로그 집계 기능은 응용 프로그램이 완료된 후 작업자 노드의 모든 컨테이너에서 로그를 집계하고 이를 작업자 노드별 하나의 집계 파일로 기본 파일 시스템에 저장하므로 응용 프로그램 로그에 더 명확하게 액세스할 수 있도록 지원합니다. 응용 프로그램은 수백 수천 개의 컨테이너를 사용할 수 있지만 단일 작업자 노드에서 실행되는 모든 컨테이너에 대한 로그는 항상 단일 파일로 집계되므로, 응용 프로그램에서 작업자 노드당 하나의 로그 파일만 사용합니다. 로그 집계는 HDInsight 클러스터(버전 3.0 이상)에서 기본적으로 사용하도록 설정되며 집계된 로그는 클러스터의 기본 컨테이너인 다음 위치에 있습니다.

	wasb:///app-logs/<user>/logs/<applicationId>

이 위치에서 *user*는 응용 프로그램을 시작한 사용자의 이름이고, *applicationId*는 YARN RM이 할당한 응용 프로그램의 고유 식별자입니다.

집계된 로그는 컨테이너별로 인덱싱된 [이진 형식][binary-format]인 [TFile][T-file]로 작성되므로 직접 읽을 수 없습니다. YARN에서는 관심 있는 응용 프로그램 또는 컨테이너에 대한 이러한 로그를 일반 텍스트로 덤프하는 CLI 도구를 제공합니다. RDP를 통해 연결한 후 클러스터 노드에서 직접 다음 YARN 명령 중 하나를 실행하여 이러한 로그를 일반 텍스트로 볼 수 있습니다.

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

다음 섹션에서는 RDP를 사용하여 HDInsight 클러스터에 연결하지 않고도 응용 프로그램별 또는 컨테이너별 로그에 프로그래밍 방식으로 액세스하는 방법에 대해 설명합니다.

## <a name="enumerate-and-download"></a>프로그래밍 방식으로 응용 프로그램 열거 및 로그 다운로드

다음 코드 샘플을 사용하려면 최신 버전의 HDInsight .NET SDK를 다운로드하여 위에서 설명한 필수 조건을 충족해야 합니다. 위에 제공된 지침을 참조하세요.

아래 코드에서는 새 API를 사용하여 응용 프로그램을 열거하고 완료된 응용 프로그램에 대한 로그를 다운로드하는 방법을 보여 줍니다.

> [AZURE.NOTE]아래 API는 3.1.1.374 이상 버전을 사용하여 "실행 중"인 Hadoop 클러스터에서만 작동합니다. 다음 지시문을 추가합니다.

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

이 지시문은 아래 코드에서 새로 정의된 API를 참조합니다. 다음 코드 조각은 구독에서 "실행 중"인 클러스터에 대해 응용 프로그램 기록 클라이언트를 만듭니다.

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


이제 응용 프로그램 기록 클라이언트를 사용하여 완료된 응용 프로그램을 나열하고 기준에 따라 응용 프로그램을 필터링하고 관련 응용 프로그램 로그를 다운로드할 수 있습니다. 다음 코드 조각에서는 이 작업을 프로그래밍 방식으로 수행하는 방법을 보여 줍니다.

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

위의 코드는 응용 프로그램 기록 클라이언트를 사용하여 관심 있는 응용 프로그램을 나열/검색한 다음 이러한 응용 프로그램의 로그를 로컬 폴더로 다운로드합니다.

또는 아래 코드 조각은 응용 프로그램 ID를 아는 응용 프로그램의 로그를 다운로드합니다. 응용 프로그램 ID는 RM이 할당한 응용 프로그램의 전역 고유 식별자입니다. 이 식별자는 RM의 시작 시간과 RM에서 제출된 응용 프로그램 수를 나타내는 단조롭게 증가하는 카운터를 함께 사용하여 생성됩니다. 응용 프로그램 ID의 형식은 "application_&lt;RM-start-time&gt;_&lt;Counter&gt;"입니다. 응용프로그램 ID와 작업 ID는 서로 다릅니다. 작업 ID는 MapReduce 프레임워크에 고유한 개념인 반면 응용 프로그램 ID는프레임워크와 상관없는 YARN 개념입니다. YARN에서 작업 ID는 RM에 제출된 MapReduce 응용 프로그램의 응용 프로그램 AM에서 처리하는 특정 MapReduce 작업을 식별합니다.

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

필요한 경우 아래 표시된 응용 프로그램에서 사용하는 각 컨테이너(또는 특정 컨테이너)의 로그를 다운로드할 수도 있습니다.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
 

<!---HONumber=July15_HO4-->