<properties 
	pageTitle="데이터 팩터리 SDK를 사용하여 Azure 데이터 팩터리 만들기, 모니터링 및 관리" 
	description="프로그래밍 방식으로 만들기, 모니터링 및 데이터 팩터리 SDK를 사용 하 여 Azure 데이터 팩터리를 관리 하는 방법에 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 데이터 팩터리 .NET SDK를 사용하여 Azure 데이터 팩터리 만들기, 모니터링 및 관리
## 개요
데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 Azure 데이터 팩터리를 만들고, 모니터링하며, 관리할 수 있습니다. 이 문서에는 데이터 팩터리를 만들고 모니터링하는 샘플 .NET 콘솔 응용 프로그램을 만들 수 있는 연습이 포함되어 있습니다. 참조 [데이터 팩터리 클래스 라이브러리 참조][adf-class-library-reference] 데이터 팩터리.NET SDK에 대 한 세부 정보에 대 한 합니다.



## 필수 조건

- Visual Studio 2012 또는 2013
- 다운로드 및 설치 [Azure.NET SDK][azure-developer-center]
- Azure 데이터 팩터리용 NuGet 패키지 다운로드 및 설치. 지침은 연습에 있습니다.

## 연습
1. Visual Studio 2012 또는 2013을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.
	<ol type="a">
	<li>시작 <b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>.</li>
	<li>클릭 하 여 <b>파일</b>, 가리킨 <b>새로</b>, 클릭 하 고 <b>프로젝트</b>.</li> 
	<li>확장 하 고 <b>템플릿</b>, 선택 및 <b>Visual C#</b>. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.</li> 
	<li>선택 <b>콘솔 응용 프로그램</b> 오른쪽에 프로젝트 형식 목록에서.</li>
	<li>입력 <b>DataFactoryAPITestApp</b> 에 대 한는 <b>이름</b>.</li> 
	<li>선택 <b>C:\ADFGetStarted</b> 에 대 한는 <b>위치</b>.</li>
	<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
</ol>
2. 클릭 하 여 <b>도구</b>, 가리킨 <b>NuGet 패키지 관리자</b>, 를 클릭 하 고 <b>패키지 관리자 콘솔</b>.
3.	에 <b>패키지 관리자 콘솔</b>, -하나씩에서 다음 명령을 실행 합니다.</b>합니다. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 다음 추가 **appSetttings** 섹션에 **App.config** 파일입니다. 이러한 도우미 메서드에 의해 사용 됩니다: **GetAuthorizationHeader**. 

	**SubscriptionId**와 **ActiveDirectoryTenantId**의 값을 Azure 구독 및 테넌트 ID로 바꿉니다. 실행 하 여 이러한 값을 얻을 수 **Get AzureAccount** 에서 Azure PowerShell (해야 Add-azureaccount를 사용 하 여 먼저 로그인)입니다.
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. 다음 추가 **를 사용 하 여** 프로젝트에서 소스 파일 (Program.cs)에 문의 합니다.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. 인스턴스를 만드는 다음 코드를 추가 **DataPipelineManagementClient** 클래스에 **Main** 메서드. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 입력 및 출력 테이블과 파이프라인을 만듭니다. 또한 이 개체를 사용하여 런타임에 테이블의 조각을 모니터링합니다.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. 만드는 다음 코드를 추가 **데이터 팩터리** 에 **Main** 메서드.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. 만드는 다음 코드를 추가 **서비스 연결** 에 **Main** 메서드.
	> [AZURE.NOTE]**계정 이름****계정 키****ConnectionString** 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. 만드는 다음 코드를 추가 **입력 및 출력 테이블** 에 **Main** 메서드. 

	 **FolderPath** 입력된 blob으로 설정 됩니다 **adftutorial /** 여기서 **adftutorial** blob 저장소에서 컨테이너의 이름입니다. 이 컨테이너에 Azure blob 저장소에 없는 경우이 이름을 가진 컨테이너 만들기: **adftutorial** 및 컨테이너에 텍스트 파일을 업로드 합니다.
	
	출력 blob에 대 한 FolderPath로 설정 된 참고: **adftutorial/apifactoryoutput {조각화} /** 여기서 **조각** 은 동적으로 계산 된 값에 따라 **SliceStart** (시작 날짜-시간 조각의 각.)

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. 다음 코드를 추가 **를 만들고 파이프라인 활성화** 에 **Main** 메서드. 이 파이프라인에는 **CopyActivity** 여기에 걸리는 **BlobSource** 소스로 및 **BlobSink** 싱크도 합니다. 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. 다음 도우미 메서드를 사용 하 여 추가 **Main** 메서드는 **프로그램** 클래스. 이 메서드를 사용 하면 제공할 수 있는 대화 상자를 팝 **사용자 이름** 및 **암호** 를 사용 하는 Azure 포털에 로그인 합니다.
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. 다음 코드를 추가 하는 **Main** 출력 테이블의 데이터 조각의 상태를 가져오려면 메서드. 이 샘플에서는 조각만 필요합니다.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. 다음 코드를 실행 하는 데이터 조각 조각에 대 한 세부 정보 추가 **Main** 메서드.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. 콘솔 응용 프로그램을 빌드합니다. 클릭 하 여 **빌드** 클릭 하 여 확인 하 고 메뉴에서 **솔루션 빌드**.
16. Azure Blob 저장소의 adftutorial 컨테이너에 하나 이상의 파일이 있는지 확인합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 Emp.txt 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

        John, Doe
		Jane, Doe
	 
17. 클릭 하 여 샘플을 실행 **디버그** -> **디버깅 시작** 메뉴.
18. 사용 하 여 Azure 미리 보기 포털 확인 하는 데이터 팩터리: **APITutorialFactory** 다음 아티팩트를 사용 하 여 만들어집니다. 
	- 서비스 연결: **LinkedService_AzureStorage** 
	- 테이블: **TableBlobSource** 및 **TableBlobDestination**.
	- 파이프라인: **PipelineBlobSample** 
18. 출력 파일에서 만들어졌는지 확인은 **apifactoryoutput** 폴더에는 **adftutorial** 컨테이너입니다.


## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는.NET 클래스 라이브러리, cmdlet, JSON 스크립트, 함수 등에 대 한 포괄적인 참조 콘텐츠 중... 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/

<!---HONumber=GIT-SubDir--> 