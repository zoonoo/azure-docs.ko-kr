<properties 
	pageTitle="데이터 팩터리 SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리" 
	description="데이터 팩터리 SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하고, 관리하는 방법을 알아봅니다." 
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
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# 데이터 팩터리 .NET SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리
## 개요
데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하며, 관리할 수 있습니다. 이 문서에는 데이터 팩터리를 만들고 모니터링하는 샘플 .NET 콘솔 응용 프로그램을 만들 수 있는 연습이 포함되어 있습니다. 데이터 팩터리 .NET SDK에 대한 자세한 내용은 [데이터 팩터리 클래스 라이브러리 참조][adf-class-library-reference]를 참조하세요.



## 필수 조건

- Visual Studio 2012 또는 2013
- [Azure .NET SDK][azure-developer-center] 다운로드 및 설치
- Azure Data Factory용 NuGet 패키지 다운로드 및 설치. 지침은 연습에 있습니다.

## 연습
1. Visual Studio 2012 또는 2013을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.
	<ol type="a">
	<li><b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>을 시작합니다.</li>
	<li><b>파일</b>을 클릭하고 <b>새로 만들기</b>를 가리킨 다음 <b>프로젝트</b>를 클릭합니다.</li> 
	<li><b>템플릿</b>을 확장하고 <b>Visual C#</b>을 선택합니다. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.</li> 
	<li>오른쪽의 프로젝트 형식 목록에서 <b>콘솔 응용 프로그램</b>을 선택합니다.</li>
	<li><b>DataFactoryAPITestApp</b>을 <b>이름</b>으로 입력합니다.</li> 
	<li><b>C:\ADFGetStarted</b>를 <b>위치</b>로 선택합니다.</li>
	<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
</ol>
2. <b>도구</b>를 클릭하고 <b>NuGet 패키지 관리자</b>를 가리킨 다음 <b>패키지 관리자 콘솔</b>을 클릭합니다.
3.	<b>패키지 관리자 콘솔</b>에서 다음 명령을 하나씩 실행합니다.</b> 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 다음 **appSetttings** 섹션을 **App.config** 파일에 추가합니다. 이는 도우미 메서드 **GetAuthorizationHeader**에서 사용됩니다. 

	**SubscriptionId**와 **ActiveDirectoryTenantId**의 값을 Azure 구독 및 테넌트 ID로 바꿉니다. Azure PowerShell에서 **Get-AzureAccount**를 실행하여 이러한 값을 가져올 수 있습니다(먼저 Add-AzureAccount를 사용하여 로그인해야 할 수 있음).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="<subscription ID>" />
    		<add key="ActiveDirectoryTenantId" value="<tenant ID" />
		</appSettings>
6. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. **DataPipelineManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 **Main** 메서드에 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 입력 및 출력 테이블과 파이프라인을 만듭니다. 또한 이 개체를 사용하여 런타임에 테이블의 조각을 모니터링합니다.    

        // create data factory management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

7. **데이터 팩터리**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

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

8. **연결된 서비스**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.
	> [AZURE.NOTE]**ConnectionString**에 Azure 저장소 계정의 **계정 이름** 및 **계정 키**를 사용합니다.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=spestore;AccountKey=4VwviDOId32nYKABQy9NHsMG0vC/CXx9iuR02HJdGL+0kieqHqbT3ap+bM/c+aGnGoA7SqkwNFq90hqV1bmV0w==")
                    )
                }
            }
        );
9. **입력 및 출력 테이블**을 만드는 다음 코드를 **Main** 메서드에 추가합니다. 

	입력 Blob의 **FolderPath**는 **adftutorial/**로 설정됩니다. 여기서 **adftutorial**은 Blob 저장소에 있는 컨테이너의 이름입니다. Azure Blob 저장소에 이 컨테이너가 없는 경우 이름이 **adftutorial**인 컨테이너를 만들고 텍스트 파일을 컨테이너로 업로드합니다.
	
	출력 Blob의 FolderPath는 **adftutorial/apifactoryoutput/{Slice}**로 설정됩니다. 여기서 **Slice**는 **SliceStart**(각 조각의 시작 날짜-시간) 값을 기반으로 동적으로 계산됩니다.

 
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
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
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

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
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
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. **파이프라인을 만들고 활성화**하는 다음 코드를 **Main** 메서드에 추가합니다. 이 파이프라인에는 **BlobSource**를 원본으로 사용하고 **BlobSink**를 싱크로 사용하는 **CopyActivity**가 포함되어 있습니다.

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

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Table_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                        
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. **Main** 메서드에 사용되는 다음 도우미 클래스를 **Program** 클래스에 추가합니다. 이 메서드는 Azure 포털에 로그인하는 데 사용하는 **사용자 이름** 및 **암호**를 입력할 수 있는 대화 상자를 표시합니다.
 
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
 
13. **Main** 메서드에 다음 코드를 추가하여 출력 테이블의 데이터 조각 상태를 가져옵니다. 이 샘플에서는 조각만 필요합니다.
 
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

14. **(선택 사항)** 데이터 조각의 실행 정보를 가져오는 다음 코드를 **Main** 메서드에 추가합니다.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Table_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
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

15. 콘솔 응용 프로그램을 빌드합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다. **ConfigurationManager** 클래스에 대한 오류를 가져오는 경우 참조를 **System.Configuration** 어셈블리에 추가하고 다시 빌드를 시도합니다.
16. Azure Blob 저장소의 adftutorial 컨테이너에 하나 이상의 파일이 있는지 확인합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 Emp.txt 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

        John, Doe
		Jane, Doe
	 
17. 메뉴에서 **디버그** -> **디버깅 시작**을 클릭하여 샘플을 실행합니다. **데이터 조각의 실행 정보 가져오기**가 표시되면 몇 분 동안 기다린 다음 **ENTER** 키를 누릅니다.
18. Azure Preview 포털을 사용하여 데이터 팩터리 **APITutorialFactory**가 다음 아티팩트로 생성되었는지 확인합니다. 
	- 연결된 서비스: **LinkedService_AzureStorage** 
	- 테이블: **TableBlobSource** 및 **TableBlobDestination**
	- 파이프라인: **PipelineBlobSample** 
18. 출력 파일이 **adftutorial** 컨테이너의 **apifactoryoutput** 폴더에 만들어졌는지 확인합니다.


## 참고 항목

문서 | 설명
------ | ---------------
[Azure Data Factory 개발자 참조][developer-reference] | 개발자 참조에는 .NET 클래스 라이브러리, cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=July15_HO4-->