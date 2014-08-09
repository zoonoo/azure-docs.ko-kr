<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Azure에서 성능 카운터 사용
==========================

Azure 응용 프로그램에서 성능 카운터를 사용하여 시스템 병목 상태, 미세 조정 시스템, 응용 프로그램 성능을 확인하는 데 도움을 줄 수 있는 데이터를 수집할 수 있습니다. Windows Server 2008, Windows Server 2012, IIS 및 ASP.NET에서 사용할 수 있는 성능 카운터를 수집하여 Azure 응용 프로그램의 상태를 확인하는 데 사용할 수 있습니다.

이 항목에서는 diagnostics.wadcfg 구성 파일을 사용하여 응용 프로그램에서 성능 카운터를 사용하도록 설정하는 방법에 대해 설명합니다. [Azure 관리 포털](http://manage.windowsazure.com)에서의 응용 프로그램 성능 모니터링에 대한 내용은 [클라우드 서비스를 모니터링하는 방법](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)을 참조하십시오. 문제를 해결하고 Azure 응용 프로그램을 최적화하기 위한 로깅 및 추적 전략 만들기와 진단 및 기타 기술 사용에 대한 더 많은 세부 지침은 [Azure 응용 프로그램 개발 문제 해결 모범 사례](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh771389.aspx)(영문)를 참조하십시오.

이 작업에는 다음 단계가 포함됩니다.

-   [필수 조건](#prereqs)
-   [1단계: 성능 카운터에서 데이터 수집 및 저장](#step1)
-   [2단계: (옵션) 사용자 지정 성능 카운터 만들기](#step2)
-   [3단계: 성능 카운터 데이터 쿼리](#step3)
-   [다음 단계](#nextsteps)
-   [추가 리소스](#additional)

필수 조건
---------

이 항목은 진단 모니터를 응용 프로그램으로 가져왔으며 diagnostics.wadcfg 구성 파일을 Visual Studio 솔루션에 추가했다고 가정합니다. 자세한 내용은 [Azure에서 진단 사용](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)의 1단계와 2단계를 참조하십시오.

1단계: 성능 카운터에서 데이터 수집 및 저장
------------------------------------------

diagnostics.wadcfg 파일을 Visual Studio 솔루션에 추가하면 Azure 응용 프로그램에서 성능 카운터 데이터의 수집 및 저장을 구성할 수 있습니다. 이렇게 하려면 성능 카운터를 diagnostics.wadcfg 파일에 추가하면 됩니다. 성능 카운터를 포함한 진단 데이터는 인스턴스에서 먼저 수집됩니다. 그런 다음 그 데이터는 Azure 테이블 서비스의 **WADPerformanceCountersTable** 테이블에 보관되므로 응용 프로그램에서 저장소 계정도 지정해야 합니다. 계산 에뮬레이터에서 응용 프로그램을 로컬로 테스트하는 중이면 진단 데이터도 저장소 에뮬레이터에 로컬로 저장할 수 있습니다. 진단 데이터를 저장하기 전에 먼저 [Azure 관리 포털](http://manage.windowsazure.com)로 이동하여 저장소 계정을 만들어야 합니다. 외부 대역폭 비용을 지불하지 않고 대기 시간을 줄이기 위해 Azure 응용 프로그램과 동일한 지리적 위치에 저장소 계정을 두는 것이 가장 좋습니다.

### diagnostics.wadcfg 파일에 성능 카운터 추가

수집할 수 있는 성능 카운터는 많으며, 다음 예제는 웹 및 작업자 역할 모니터링에 권장되는 일부 성능 카운터를 보여 줍니다.

diagnostics.wadcfg 파일을 열고 **DiagnosticMonitorConfiguration** 요소에 다음을 추가합니다.

     	<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />
        <!-- Use the Process(w3wp) category counters in a web role -->
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />
        <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" /> 
		--\>
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

**bufferQuotaInMB** 특성은 데이터 수집 형식(Azure 로그, IIS 로그 등)에 사용할 수 있는 파일 시스템 저장소의 최대량을 지정합니다. 기본값은 0입니다. 할당량에 도달하면 새 데이터가 추가될 때 가장 오래된 데이터가 삭제됩니다. 모든 **bufferQuotaInMB** 속성의 합계는 **OverallQuotaInMB** 특성 값보다 커야 합니다. 진단 데이터를 수집하는 데 필요한 저장소 양을 결정하는 자세한 논의는 [Azure 응용 프로그램 개발 문제 해결 모범 사례](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh771389.aspx)(영문)의 WAD 설정 섹션을 참조하십시오.

**scheduledTransferPeriod** 특성은 데이터의 예약 전송 사이의 간격을 지정합니다(가장 가까운 시간(분)으로 반올림됨). 다음 예제에서는 PT30M(30분)으로 설정됩니다. 전송 간격을 1분처럼 작은 값으로 설정하면 프로덕션에서 응용 프로그램의 성능에 악영향을 주지만 테스트할 때 진단 작업을 빠르게 보는 데는 유용할 수 있습니다. 예약 전송 간격은 인스턴스에서 진단 데이터를 덮어쓰지 않을 정도로 작고 응용 프로그램의 성능에 영향을 주지 않을 정도로 커야 합니다.

**counterSpecifier** 특성은 수집할 성능 카운터를 지정합니다.

**sampleRate** 특성은 성능 카운터가 샘플링되어야 하는 속도를 지정합니다(이 경우 30초).

수집할 성능 카운터를 추가했으면 변경 내용을 diagnostics.wadcfg 파일에 저장합니다. 그다음에 진단 데이터를 보관할 저장소 계정을 지정해야 합니다.

### 저장소 계정 지정

진단 정보를 Azure 저장소 계정에 보관하려면 서비스 구성(ServiceConfiguration.cscfg) 파일에서 연결 문자열을 지정해야 합니다. Azure Tools for Visual Studio 버전 1.4(2011년 8월) 이상을 사용하여 로컬과 클라우드에 대해 다른 구성 파일(ServiceConfiguration.cscfg)을 가질 수 있습니다. 프로덕션에 대해 별도의 구성 파일을 유지하면서 로컬 테스팅에 저장소 에뮬레이터를 무료로 사용할 수 있으므로 서비스 구성이 여러 개이면 진단에 유용합니다.

연결 문자열을 설정하려면

1.  원하는 텍스트 편집기를 사용하여 ServiceConfiguration.Cloud.cscfg 파일을 열고 저장소 계정에 대한 연결 문자열을 설정합니다.

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    **AccountName** 및 **AccountKey** 값은 저장소 계정 대시보드의 관리 포털에서 **키 관리** 아래에 있습니다.

2.  ServiceConfiguration.Cloud.cscfg 파일을 저장합니다.
3.  ServiceConfiguration.Local.cscfg 파일을 열고 **UseDevelopmentStorage**가 true(기본값)로 설정되어 있는지 확인합니다.

        <ConfigurationSettings>
			<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>

    연결 문자열이 설정되면 응용 프로그램이 배포될 때 응용 프로그램에서 진단 데이터를 저장소 계정에 보관합니다.

4.  프로젝트를 저장하고 빌드한 다음 응용 프로그램을 배포합니다.

2단계: (옵션) 사용자 지정 성능 카운터 만들기
--------------------------------------------

미리 정의한 성능 카운터 외에도 웹 또는 작업자 역할을 모니터링하기 위해 사용자 지정 성능 카운터를 추가할 수 있습니다. 사용자 지정 성능 카운터는 응용 프로그램별 동작을 추적하고 모니터링하는 데 사용할 수 있으며, 관리자 권한으로 시작 작업, 웹 역할 또는 작업자 역할에서 만들거나 삭제할 수 있습니다.

다음 단계를 수행하여 "\\MyCustomCounterCategory\\MyButton1Counter"라는 간단한 사용자 지정 성능 카운터를 만듭니다.

1.  응용 프로그램의 서비스 정의 파일(CSDEF)을 엽니다.
2.  **Runtime** 요소를 **WebRole** 또는 **WorkerRole** 요소에 추가하여 관리자 권한으로 실행할 수 있도록 합니다.

        <Runtime executionContext="elevated" />

3.  파일을 저장합니다.
4.  diagnostics.wadcfg 파일을 열고 **DiagnosticMonitorConfiguration** 요소에 다음을 추가합니다.

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  파일을 저장합니다.
6.  **base.OnStart**를 호출하기 전에 역할의 **OnStart** 메서드에 사용자 지정 성능 카운터 범주를 만듭니다. 다음 C\# 예제는 사용자 지정 범주가 없는 경우 만듭니다.

         public override bool OnStart()
        {
        if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
        {
           CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

           // add a counter tracking user button1 clicks
           CounterCreationData operationTotal1 = new CounterCreationData();
           operationTotal1.CounterName = "MyButton1Counter";
           operationTotal1.CounterHelp = "My Custom Counter for Button1";
           operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
           counterCollection.Add(operationTotal1);

           PerformanceCounterCategory.Create(
             "MyCustomCounterCategory",
             "My Custom Counter Category",
             PerformanceCounterCategoryType.SingleInstance, counterCollection);

           Trace.WriteLine("Custom counter category created.");
        }
        else{
           Trace.WriteLine("Custom counter category already exists.");
        }

        return base.OnStart();
        }

7.  응용 프로그램 내에서 카운터를 업데이트합니다. 다음 예제는 **Button1\_Click** 이벤트에서 사용자 지정 성능 카운터를 업데이트합니다.

        protected void Button1_Click(object sender, EventArgs e)
        {
           PerformanceCounter button1Counter = new PerformanceCounter(
              "MyCustomCounterCategory",
              "MyButton1Counter",
              string.Empty,
              false);

           button1Counter.Increment();
           this.Button1.Text = "Button 1 count: " +
              button1Counter.RawValue.ToString();
        }

8.  파일을 저장합니다.

이러한 단계가 완료되면 Azure 진단 모니터가 사용자 지정 성능 카운터 데이터를 수집합니다.

3단계: 성능 카운터 데이터 쿼리
------------------------------

응용 프로그램이 배포되고 진단 모니터가 실행되면 성능 카운터를 수집하여 해당 데이터를 Azure 저장소에 보관하기 시작합니다. **Visual Studio의 서버 탐색기**, [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/) 또는 Cerebrata의 [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)와 같은 도구를 사용하여 **WADPerformanceCountersTable** 테이블의 성능 카운터 데이터를 확인합니다. 또한 [C\#][], [Java](http://www.windowsazure.com/ko-kr/develop/java/how-to-guides/table-service/), [Node.js][], [Python](http://www.windowsazure.com/ko-kr/develop/python/how-to-guides/table-service/) 또는 [PHP](http://www.windowsazure.com/ko-kr/develop/php/how-to-guides/table-service/)를 사용하여 테이블 서비스를 프로그래밍 방식으로 쿼리할 수도 있습니다.

다음 C\# 예제는 **WADPerformanceCountersTable** 테이블에 대한 간단한 쿼리를 보여 주고 진단 데이터를 CSV 파일에 저장합니다. 성능 카운터를 CSV 파일에 저장하고 나면 Microsoft Excel 또는 다른 도구의 그래픽 기능을 사용하여 데이터를 시각화할 수 있습니다. Azure SDK for .NET 2012년 10월 이상 버전에 포함되어 있는 Microsoft.WindowsAzure.Storage.dll에 참조를 추가해야 합니다. 어셈블리는 %Program Files%\\Microsoft SDKs\\Azure.NET SDK\\version-num\\ref\\ 디렉터리에 설치됩니다.

		using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Get the connection string. When using Azure Cloud Services, it is recommended 
        // you store your connection string using the Azure service configuration
        // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type 
        // to retrieve your storage connection string.  If you're not using Cloud Services, it's
        // recommended that you store the connection string in your web.config or app.config file.
        // Use the ConfigurationManager type to retrieve your storage connection string.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Get a reference to the storage account using the connection string.  You can also use the development storage account (Storage Emulator)
        // for local debugging.              
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

        // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
        CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

        // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
        TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
            .Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
                    TableOperators.And,
                    TableQuery.CombineFilters(                        
                        TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")                        
                    )
                )
            );

        // Execute the table query.
        IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

        // Process the query results and build a CSV file.
        StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");
            
        foreach (PerformanceCountersEntity entity in result)
        {
            sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + "," 
				+ entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
        }

        StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
        sw.Write(sb.ToString());
        sw.Close();

엔터티는 **TableEntity**에서 파생된 사용자 지정 클래스를 사용하여 C\# 개체에 매핑됩니다. 다음 코드는 **WADPerformanceCountersTable** 테이블의 성능 카운터를 나타내는 엔터티 클래스를 정의합니다.

     public class PerformanceCountersEntity : TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

다음 단계
---------

이제 성능 카운터 수집의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 문제 해결 시나리오를 구현하는 방법을 알아보십시오.

-   [Azure 응용 프로그램 개발 문제 해결 모범 사례(영문)](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh771389.aspx)
-   [클라우드 서비스를 모니터링하는 방법](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [자동 크기 조정 응용 프로그램 블록을 사용하는 방법](http://www.windowsazure.com/ko-kr/develop/net/how-to-guides/autoscaling/)
-   [탄력적이고 복원력이 뛰어난 클라우드 앱 빌드(영문)](http://msdn.microsoft.com/ko-kr/library/hh680949(PandP.50).aspx)

추가 리소스
-----------

-   [Azure에서 진단 사용](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)
-   [Azure 진단을 사용하여 로깅 데이터 수집](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433048.aspx)
-   [Azure 응용 프로그램 디버깅](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee405479.aspx)


  [Overview of Creating and Using Performance Counters in an Azure Application]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh411520.aspx
  [Prerequisites]: #prereqs
  [Step 1: Collect and store data from performance counters]: #step1
  [Step 2: (Optional) Create custom performance counters]: #step2
  [Step 3: Query performance counter data]: #step3
  [Next Steps]: #nextsteps
  [Additional Resources]: #additional
  
  [Collecting Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433048.aspx
  [Debugging an Azure Application]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee405479.aspx
  [How to Use the Autoscaling Application Block]: http://www.windowsazure.com/ko-kr/develop/net/how-to-guides/autoscaling/
  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh771389.aspx
  [Enabling Diagnostics in Azure]: https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/
  [How to use the Table Storage Service]: http://www.windowsazure.com/ko-kr/develop/net/how-to-guides/table-services/
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  
  [Java]: http://www.windowsazure.com/ko-kr/develop/java/how-to-guides/table-service/
  [Python]: http://www.windowsazure.com/ko-kr/develop/python/how-to-guides/table-service/
  [PHP]: http://www.windowsazure.com/ko-kr/develop/php/how-to-guides/table-service/
  
  [Building Elastic and Resilient Cloud Apps]: http://msdn.microsoft.com/ko-kr/library/hh680949(PandP.50).aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [How to Monitor Cloud Services]: https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/
