<properties
	pageTitle="자습서 - Azure 배치 .NET 라이브러리 시작 | Microsoft Azure"
	description="Azure 배치의 기본 개념과 간단한 시나리오를 통해 배치 서비스를 개발하는 방법을 알아봅니다."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# .NET용 Azure 배치 라이브러리 시작(영문)  

Azure 배치 풀의 여러 계산 노드에서 실행되는 지원 파일 및 프로그램을 설정하는 콘솔 응용 프로그램을 만들어 Azure 배치 .NET 라이브러리로 작업을 시작합니다. 이 자습서에서 만들어진 태스크는 Azure 저장소에 업로드된 파일의 텍스트를 평가하고 해당 파일에서 가장 일반적으로 나타나는 단어를 반환합니다. 샘플은 C#로 작성되었으며 [Azure 배치 .NET 라이브러리](https://msdn.microsoft.com/library/azure/mt348682.aspx)를 사용합니다.

## 필수 조건

- 계정:

	- **Azure 계정** - 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

	- **배치 계정** - [Azure 배치 계정 만들기 및 관리](batch-account-create-portal.md)를 참조하세요.

	- **저장소 계정** - [Azure 저장소 계정 정보](../storage-create-storage-account.md)의 **저장소 계정 만들기** 섹션을 참조하세요. 이 자습서에서는 **testcon1**이라는 컨테이너를 이 계정에 만듭니다.

- Visual Studio 콘솔 응용 프로그램 프로젝트:

	1.  Visual Studio를 열고 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

	2.	**Windows**의 **Visual C#**에서 **콘솔 응용 프로그램**을 클릭하고 프로젝트 이름을 **GettingStarted**로 지정한 후 솔루션 이름을 **AzureBatch**로 지정하고 **확인**을 클릭합니다.

- NuGet 어셈블리:

	1. Visual Studio에서 프로젝트를 만든 후에 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 **Azure.Batch**를 검색하고 **설치**를 클릭하여 Microsoft Azure 배치 패키지와 종속성을 설치합니다.

	2. 온라인에서 **WindowsAzure.Storage**를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

> [AZURE.TIP]이 자습서에서는 [Azure 배치 기능 개요](batch-api-basics.md)에 설명된 핵심적인 배치 개념 중 일부를 활용하며, 이 문서에서 배치의 새로운 기능에 대해 읽어볼 것을 권장합니다.

## 1단계: 지원 파일 만들기 및 업로드

응용 프로그램을 지원하기 위해 Azure 저장소에 컨테이너를 만들고, 텍스트 파일을 만들고, 텍스트 파일 및 지원 파일을 컨테이너에 업로드합니다.

### 저장소 연결 문자열 설정

1. GettingStarted 프로젝트의 App.config 파일을 열고 *&lt;appSettings&gt;* 요소를 *&lt;configuration&gt;*에 추가합니다.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	다음 값을 바꿉니다.

	- **[account-name]** - 이전에 만든 저장소 계정의 이름으로 바꿉니다.

	- **[account-key]** - 저장소 계정의 기본 키로 바꿉니다. 기본 키는 Azure Preview 포털의 저장소 페이지에서 찾을 수 있습니다.

2. App.config 파일을 저장합니다.

Azure 저장소 연결 문자열에 대해 자세히 알아보려면 [Azure 저장소 연결 문자열 구성](../storage/storage-configure-connection-string.md)을 참조하세요.

### 저장소 컨테이너 만들기

1. GettingStarted 프로젝트의 Program.cs 맨 위에 다음 using 지시문을 추가합니다.

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. *System.Configuration*를 GettingStarted 프로젝트에 대한 **솔루션 탐색기**의 **참조**에 추가합니다.

3. 저장소 연결 문자열을 가져오고, 컨테이너를 만들고, 사용 권한을 설정하는 Program 클래스에 다음 메서드를 추가합니다.

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		CreateStorage();

5. Program.cs 파일을 저장합니다.

	> [AZURE.NOTE]프로덕션 환경에서는 [공유 액세스 서명](https://msdn.microsoft.com/library/azure/ee395415.aspx)을 사용하는 것이 좋습니다.

Blob 저장소에 대한 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](../storage/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

### 처리 프로그램 만들기

1. **솔루션 탐색기**에서 **ProcessTaskData**라는 새 콘솔 응용 프로그램 프로젝트를 만듭니다.

2. Visual Studio에서 프로젝트를 만든 후에 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 **WindowsAzure.Storage**를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

3. Program.cs의 맨 위에 다음 using 지시문을 추가합니다.

		using Microsoft.WindowsAzure.Storage.Blob;

4. 파일에서 텍스트를 처리하는 Main에 다음 코드를 추가합니다.

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

5. ProcessTaskData 프로젝트를 저장하고 빌드합니다.

### 데이터 파일 만들기

1. GettingStarted 프로젝트에서 **taskdata1.txt**이라는 새 텍스트 파일을 만들고 이 파일에 다음 텍스트를 복사한 다음 파일을 저장합니다.

	비즈니스 요구 사항 때문에 유연한 리소스가 필요한 경우 Azure 가상 컴퓨터를 사용하여 확장 가능한 주문형 계산 인프라를 프로비전할 수 있습니다. 갤러리에서 Windows, Linux 및 엔터프라이즈 응용 프로그램(예: SharePoint 및 SQL Server)을 실행하는 가상 컴퓨터를 만들 수 있습니다. 또는 고유한 이미지를 캡처 및 사용하여 사용자 지정 가상 컴퓨터를 만들 수도 있습니다.

2. **taskdata2.txt**라는 새 텍스트 파일을 만들고 이 파일에 다음 텍스트를 복사한 다음 파일을 저장합니다.

	Azure 클라우드 서비스를 통해 강력한 응용 프로그램과 서비스를 신속하게 배포하고 관리할 수 있습니다. 응용 프로그램을 업로드하기만 하면 Azure가 프로비전 및 부하 분산부터 지속적인 가용성을 위한 상태 모니터링에 이르기까지 모든 배포 세부 작업을 처리합니다. 응용 프로그램은 업계 선두의 99.95% 월간 SLA로 지원됩니다. 인프라가 아닌 응용 프로그램에만 집중하세요.

3. **taskdata3.txt**라는 새 텍스트 파일을 만들고 이 파일에 다음 텍스트를 복사한 다음 파일을 저장합니다.

	Azure 웹 사이트는 웹 응용 프로그램을 호스트할 수 있는 확장 가능하며 안정적이고 사용하기 쉬운 환경을 제공합니다. 다양한 프레임워크 및 템플릿 중에서 선택하여 몇 초만에 웹 사이트를 만들 수 있습니다. 원하는 도구 또는 OS에서 .NET, PHP, Node.js 또는 Python을 사용하여 사이트를 개발할 수 있습니다. TFS, GitHub 및 BitBucket을 비롯한 다양한 소스 제어 옵션 중에서 선택하여 연속 통합을 설정하고 팀으로 개발할 수 있습니다. 저장소, CDN 및 SQL 데이터베이스와 같은 추가 Azure 관리되는 서비스를 활용하여 시간 경과에 따라 사이트 기능을 확장할 수 있습니다.

### 저장소 컨테이너에 파일 업로드

1. **GettingStarted** 프로젝트의 Program.cs 파일을 열고 파일을 업로드 하는 다음 메서드를 추가합니다.

		static void CreateFiles()
		{
		  CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		CreateFiles();

3. Program.cs 파일을 저장합니다.

## 2단계. 배치 계정에 풀 추가

계산 노드 풀은 태스크를 실행하려고 할 때 만들어야 하는 첫 번째 리소스 집합입니다.

1.	GettingStarted 프로젝트의 Program.cs 맨 위에 다음 using 지시문을 추가합니다.

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Azure Batch 서비스에 대한 호출을 수행하는 데 필요한 자격 증명을 설정하는 Main에 다음 코드를 추가합니다.

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	대괄호로 묶인 값을 배치 계정과 관련된 값으로 바꿉니다. 각 값은 [Azure Preview 포털](https://portal.azure.com)에서 확인할 수 있습니다. 이러한 값을 찾으려면 [Azure Preview 포털](https://portal.azure.com)에 로그인하고 다음을 수행합니다.

	- **[account-name]** - **배치 계정**을 클릭하고 이전에 만든 배치 계정을 선택합니다.
	- **[account-url]** - 배치 계정 블레이드 내에서 **속성** > **URL**을 클릭합니다.
	- **[account-key]** - 배치 계정 블레이드 내에서 **속성** > **키** > **기본 선택키**를 클릭합니다.

3.	풀을 만드는 Program 클래스에 다음 메서드를 추가합니다.

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		CreatePool(client);

5. 계정의 풀을 나열하는 Program 클래스에 다음 메서드를 추가합니다. 이렇게 하면 풀이 만들어졌는지 확인하는데 도움이 됩니다.

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		ListPools(client);

7. Program.cs 파일을 저장합니다.

## 3단계: 계정에 작업 추가

풀에서 실행되는 태스크를 관리하는 데 사용되는 작업을 만듭니다. 모든 태스크는 이 작업에 연결되어야 합니다.

1. 작업을 만드는 Program 클래스에 다음 메서드를 추가합니다.

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		CreateJob(client);

3. 계정의 작업을 나열하는 Program 클래스에 다음 메서드를 추가합니다. 이렇게 하면 작업이 만들어졌는지 확인하는데 도움이 됩니다.

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		ListJobs(client);

5. Program.cs 파일을 저장합니다.

## 4단계: 작업에 태스크 추가

작업을 만든 후에는 태스크를 작업에 추가할 수 있습니다. 각 태스크는 계산 노드에서 실행되며 텍스트 파일을 처리합니다. 이 자습서에서는 세 개의 태스크를 작업에 추가합니다.

1. 세 개의 태스크를 작업에 추가하는 Program 클래스에 다음 메서드를 추가합니다.

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	**[account-name]** - 이전에 만든 저장소 계정의 이름으로 바꿔야 합니다. 이전 예제에서 **[account-name]**의 네 인스턴스를 모두 업데이트합니다.


2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

			AddTasks(client);

3. 작업에 연결된 태스크를 나열하는 Program 클래스에 다음 메서드를 추가합니다.

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		ListTasks(client);

5. Program.cs 파일을 저장합니다.

## 5단계: 리소스 삭제

Azure에서 리소스에 대한 요금이 부과되므로 리소스가 더 이상 필요하지 않은 경우 항상 리소스를 삭제하는 것이 좋습니다.

### 태스크 삭제

1.	태스크를 삭제하는 Program 클래스에 다음 메서드를 추가합니다.

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		DeleteTasks(client);

3. Program.cs 파일을 저장합니다.

### 작업 삭제

1.	작업을 삭제하는 Program 클래스에 다음 메서드를 추가합니다.

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		DeleteJob(client);

3. Program.cs 파일을 저장합니다.

### 풀 삭제

1. 풀을 삭제하는 Program 클래스에 다음 메서드를 추가합니다.

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. 방금 추가한 메서드를 호출하는 Main에 다음 코드를 추가합니다.

		DeletePool(client);

3. Program.cs 파일을 저장합니다.

## 6단계: 응용 프로그램 실행

1. GettingStarted 프로젝트를 시작하고 컨테이너를 만들면 콘솔 창에 다음 내용이 표시됩니다.

		Created the container. Press Enter to continue.

2. Enter 키를 누르면 파일이 만들어지고 업로드되며 창에 다음과 같이 새 줄이 표시됩니다.

		Uploaded the files. Press Enter to continue.

3. Enter 키를 누르면 풀이 만들어집니다.

		Created the pool. Press Enter to continue.

4. Enter 키를 누르면 다음과 같이 새 풀 목록이 표시됩니다.

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Enter 키를 누르면 작업이 만들어집니다.

		Created the job. Press Enter to continue.

6. Enter 키를 누르면 다음과 같이 새 작업 목록이 표시됩니다.

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Enter 키를 누르면 태스크가 작업에 추가됩니다. 태스크가 추가되면 자동으로 실행됩니다.

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Enter 키를 누르면 태스크 및 태스크 상태 목록이 표시됩니다.

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. 이 때 Azure Preview 포털로 이동하여 만들어진 리소스를 확인할 수 있습니다. 리소스를 삭제하려면 프로그램이 완료될 때까지 Enter 키를 누릅니다.

## 다음 단계

1. 실행 중인 태스크의 기본 사항에 대해 알아보았으므로 응용 프로그램 요구 사항이 변경되는 경우 자동으로 계산 노드 크기를 조정하는 방법에 대해 알아봅니다. 이 작업을 수행하려면 [Azure 배치 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

2. 일부 응용 프로그램은 많은 양의 데이터를 생성하여 처리하기가 어려울 수 있습니다. 이 문제를 해결하는 한 가지 방법은 [효율적인 목록 쿼리](batch-efficient-list-queries.md)를 사용하는 것입니다.

<!---HONumber=AcomDC_1125_2015-->