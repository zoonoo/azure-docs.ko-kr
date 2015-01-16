<properties linkid="manage-services-Stream-Analytics-get-started" urlDisplayName="Get Started" pageTitle="Azure 스트림 분석을 사용하여 시작 | Azure" metaKeywords="" description="Azure 스트림 분석을 사용하여 Azure 서비스 버스 이벤트 허브에서 이벤트를 처리 및 변환하고 Azure SQL 데이터베이스에 결과를 저장합니다." metaCanonical="" services="stream analytics" documentationCenter="" title="Get started with Azure Stream Analytics" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="stream analytics" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/28/2014" ms.author="jgao" />


# Azure 스트림 분석을 사용하여 시작

Azure 스트림 분석은 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. 자세한 내용은 [Azure 스트림 분석 소개][stream.analytics.introduction] 및 [Azure 스트림 분석 설명서][stream.analytics.documentation]를 참조하세요.

스트림 분석 사용을 빠르게 시작할 수 있도록 지원하기 위해 이 자습서에서는 [Azure 서비스 버스 이벤트 허브][azure.event.hubs.documentation]의 장치 온도 판독 데이터를 사용하고 데이터를 처리한 후 결과를 [Azure SQL 데이터베이스][azure.sql.database.documentation]로 출력하는 방법을 보여 줍니다.  다음 다이어그램에서는 입력에서 발생한 이벤트가 처리되면서 출력으로 제공되는 흐름을 보여 줍니다.
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##이벤트 허브 샘플 데이터 생성
이 자습서는 MSDN CodeGallery의 코드 샘플인 서비스 버스 이벤트 허브 시작 응용 프로그램을 활용하여 새 이벤트 허브를 만들고, 샘플 장치 온도 판독값을 생성하고, 이벤트 허브로 장치 판독 데이터를 전송합니다.

###서비스 버스 네임스페이스 만들기
이 응용 프로그램 예제에서는 기존 서비스 버스 네임스페이스에 이벤트 허브를 만듭니다.  이미 프로비전한 서비스 버스 네임스페이스를 사용하거나 아래 단계에 따라 새 네임스페이스를 만들 수 있습니다.

1.	[Azure 관리 포털][azure.management.portal]에 로그인합니다.
2.	서비스 버스 페이지의 아래쪽에서 **만들기**를 클릭 하 고는 네임스페이스를 만드는 지침을 따릅니다. 유형으로 **메시징**을 사용합니다.
3.	새로 만든 네임스페이스를 클릭하고 페이지 아래쪽에서 **연결 정보**를 클릭합니다.
4.	연결 문자열을 복사합니다. 이 자습서의 뒷부분에서 필요합니다.

###Azure 저장소 계정 만들기

이 응용 프로그램 예제에는 응용 프로그램 상태를 유지 관리하기 위한 Azure 저장소 계정 또는 저장소 에뮬레이터가 필요합니다. 기존 저장소 계정을 사용하거나 아래 단계에 따라 하나 만들 수 있습니다. 

1.	포털에서 **새로 만들기**, **데이터 서비스**, **저장소**, **빠른 생성**을 클릭하여 새 저장소 계정을 만들고 지침을 따릅니다.
2.	새로 만든 저장소 계정을 선택하고 페이지 아래쪽에서 **액세스 키 관리**를 클릭합니다.
3.	저장소 계정 이름 및 액세스 키 중 하나를 복사합니다.

###이벤트 허브 샘플 데이터 생성

1.	[Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097)을 다운로드하고 워크스테이션에 압축을 풉니다.
2.	Visual Studio에서 **EventHubSample.sln** 솔루션 파일을 엽니다.
3.	**app.config**를 엽니다.
4.	서비스 버스 및 저장소 계정 연결 문자열을 모두 지정합니다. 키 이름은 **Microsoft.ServiceBus.ConnectionString** 및 **AzureStorageConnectionString**입니다. 저장소 계정 연결 문자열은 다음과 같은 형식이 됩니다. 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	솔루션을 빌드합니다.
6.	휴지통 폴더에서 응용 프로그램을 실행합니다.  사용법은 다음과 같습니다. 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	다음 예제에서는 **16**개의 파티션이 있는 **devicereadings**라는 새 이벤트 허브를 만든 다음 이벤트 허브에 **200**개의 이벤트를 전송합니다. 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###이벤트 허브 공유 액세스 정책 만들기
네임스페이스 내의 모든 항목에 연결 하는 데 사용할 수 있는 서비스 버스 네임스페이스에 대한 공유 액세스 정책이 이미 있는 경우 최고의 보안을 위해 이벤트 허브용 정책을 따로 만들 것입니다.

1.	포털의 서비스 버스 작업 영역에서 서비스 버스 네임스페이스 이름을 클릭합니다.
2.	페이지 위쪽에서 **이벤트 허브**를 클릭합니다.
3.	이 자습서에 대한 이벤트 허브인 **devicereadings**를 클릭합니다.
4.	페이지 위쪽에서 **구성**을 클릭합니다.
5.	공유 액세스 정책에서 **관리** 권한을 사용하여 새 정책을 만듭니다.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	페이지 아래쪽에서 **저장**을 클릭합니다.
7.	이벤트 허브가 스트림 분석 작업과는 다른 구독에 있는 경우 나중에 사용할 수 있게 연결 정보를 복사하고 저장해야 합니다.  이렇게 하려면 **대시보드**를 클릭하고 페이지 아래쪽에서 **연결 정보**를 클릭한 후 연결 문자열을 저장합니다.


##출력 데이터를 저장하기 위해 Azure SQL 데이터베이스 준비
Azure 스트림 분석은 Azure SQL 데이터베이스, Azure Blob 저장소 및 Azure 이벤트 허브에 데이터를 출력할 수 있습니다. 이 자습서에서는 Azure SQL 데이터베이스로 출력하는 작업을 정의합니다. 자세한 내용은 Microsoft Azure SQL 데이터베이스 시작을 참조하세요.

###Azure SQL 데이터베이스 만들기
이 자습서에 사용할 Azure SQL 데이터베이스가 이미 있는 경우 이 섹션을 건너뛰세요.

1.	관리 포털에서 **새로 만들기**, **데이터 서비스**, **SQL 데이터베이스**, **빠른 생성을 차례로 클릭합니다**.  기존 또는 새 SQL 데이터베이스 서버의 데이터베이스 이름을 지정합니다.
2.	새로 만든 데이터베이스 선택
3.	**대시보드**를 클릭하고 페이지의 오른쪽 창에서 **연결 문자열 표시**를 클릭합니다. 그런 다음 **ADO.NET** 연결 문자열을 복사합니다. 이 자습서의 뒷부분에서 필요합니다.  
4.	서버 수준 방화벽 설정을 통해 데이터베이스에 연결할 수 있는지 확인합니다.  서버의 구성 탭에서 새 IP 규칙을 추가하면 이렇게 할 수 있습니다. 동적 IP를 처리하는 방법을 비롯한 자세한 내용은 [http://msdn.microsoft.com/ko-kr/library/azure/ee621782.aspx](http://msdn.microsoft.com/ko-kr/library/azure/ee621782.aspx)를 참조하세요.

###출력 테이블 만들기
1.	Visual Studio 또는 SQL Server Management Studio를 엽니다.
2.	Azure SQL 데이터베이스에 연결합니다.
3.	다음 T-SQL 문을 사용하여 데이터베이스에 대한 두 테이블을 만듭니다.

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] 데이터를 삽입하려면 모든 SQL 데이터베이스 테이블에 클러스터형된 인덱스가 필요합니다.
	   
##스트림 분석 작업 만들기

Azure 서비스 버스 이벤트 허브, Azure SQL 데이터베이스 및 출력 테이블을 만든 경우 스트림 분석 작업을 만들 준비가 된 것입니다.

###스트림 분석 작업 프로비전
1.	관리 포털에서 **새로 만들기**, **데이터 서비스**, **스트림 분석**, **빠른 생성을 차례로 클릭합니다**. 
2.	다음 값을 지정하고 **스트림 분석 작업 만들기**를 클릭합니다.

	- **작업 이름**: 작업 이름을 입력합니다. 예를 들면 **DeviceTemperatures**를 입력합니다.
	- **지역**: 작업을 실행할 지역을 선택합니다. Azure 스트림 분석은 미리 보기 동안 2개 지역에서만 사용할 수 있습니다. 자세한 내용은 [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]를 참조하세요. 더 나은 성능을 보장하고 비용 부담 없이 지역 간에 데이터를 전송하려면 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋습니다.
	- **저장소 계정**: 이 지역 내에서 실행되는 모든 스트림 분석 작업에 대한 모니터링 데이터를 저장하는 데 사용할 저장소 계정을 선택합니다. 기존 저장소 계정을 선택하거나 새 계정을 만들 수 있습니다.
	
3.	왼쪽 창에서 **스트림 분석**을 클릭하여 스트림 분석 작업을 표시합니다.

	![][img.stream.analytics.portal.button]
 
	새 작업은 **시작되지 않음** 상태로 표시됩니다.  페이지 아래쪽에 있는 **시작** 단추는 사용할 수 없게 설정됩니다. 작업을 시작하려면 먼저 작업 입력, 출력, 쿼리 등을 구성해야 합니다. 

###작업 입력 지정

1.	작업 이름을 클릭합니다.
2.	페이지 위쪽에서 **입력**을 클릭하고 **입력 추가**를 클릭합니다. 열리는 대화 상자에서 다양한 단계를 진행하면서 입력을 설정하게 됩니다.
3.	**데이터 스트림**을 선택한 후 오른쪽 단추를 클릭합니다.
4.	**이벤트 허브**를 선택한 후 오른쪽 단추를 클릭합니다.
5.	세 번째 페이지에서 다음 값을 입력하거나 선택합니다. 

	- **입력 별칭**: 이 작업 입력의 이름을 입력합니다. 이 이름은 나중에 쿼리에서 사용하게 됩니다.
	- **이벤트 허브**: 만든 이벤트 허브가 스트림 분석 작업과 동일한 구독에 포함된 경우 이벤트 허브가 있는 네임스페이스를 선택합니다.

		이벤트 허브가 다른 구독에 있으면 **다른 구독의 이벤트 허브 사용**을 선택하고 **서비스 버스 네임스페이스**, **이벤트 허브 이름**, **이벤트 허브 정책 이름**, **이벤트 허브 정책 키** 및 **이벤트 허브 파티션 수**를 수동으로 입력합니다.  

		>[WACOM.NOTE] 이 샘플은 기본값인 16개의 파티션을 사용합니다.
		
	- **이벤트 허브 이름**: 만든 Azure 이벤트 허브의 이름을 선택합니다. 이 자습서에서는 **devicereadings**를 사용합니다.
	- **이벤트 허브 정책 이름**: 이 자습서의 앞부분에서 만든 이벤트 허브 정책을 선택합니다.
 
	![][img.stream.analytics.config.input]

6.	오른쪽 단추를 클릭합니다.
7.	다음 값을 지정합니다.

	- **이벤트 직렬 변환기 형식**: JSON
	- **인코딩**: UTF8

8.	이 소스를 추가 하 고 스트림 분석이 이벤트 허브에 성공적으로 연결될 수 있는지 확인하려면 확인 단추를 클릭합니다.

###작업 출력 지정
1.	페이지 위쪽에서 **출력**을 클릭하고 **출력 추가**를 클릭합니다.
2.	**SQL 데이터베이스**를 선택한 후 오른쪽 단추를 클릭합니다.
3.	다음 값을 입력하거나 선택합니다.  데이터베이스에서 ADO.NET 연결 문자열을 사용하여 다음 필드를 채웁니다.

	- **SQL 데이터베이스**: 자습서 앞부분에서 만든 SQL 데이터베이스를 선택합니다.  동일한 구독에 있는 경우 드롭다운 메뉴에서 데이터베이스를 선택합니다.   그렇지 않은 경우 서버 이름 및 데이터베이스 필드를 수동으로 입력합니다. 
	- **사용자 이름**: SQL 데이터베이스 로그인 이름을 입력합니다.
	- **암호**: SQL 데이터베이스 암호를 입력합니다.
	- **테이블**: 출력을 보낼 테이블을 지정합니다.  지금은 **PassthroughReadings**를 사용합니다.

	![][img.stream.analytics.config.output]

4.	출력을 만들고 스트림 분석이 지정된 대호 SQL 데이터베이스에 성공적으로 연결될 수 있는지 확인하려면 확인 단추를 클릭합니다.

###작업 쿼리 지정
스트림 분석은 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다.  이 언어에 대한 자세한 내용은 Azure 스트림 분석 쿼리 언어 참조를 참조하세요.  

이 자습서에는 먼저 SQL 데이터베이스 테이블에 장치 온도 판독값을 출력하는 간단한 통과 쿼리가 나옵니다.

1.	페이지 위쪽에서 **쿼리**를 클릭합니다.
2.	코드 편집기에 다음을 추가합니다.

		SELECT DeviceId, Temperature FROM input
입력 소스의 이름이 앞에서 지정한 입력의 이름과 일치하는지 확인합니다.
3.	페이지 아래쪽에서 **저장**을 클릭한 후 **예**를 클릭하여 확인합니다.

##작업 시작
기본적으로 스트림 분석 작업은 작업이 시작될 때 들어오는 이벤트를 읽기 시작합니다.  이벤트 허브에 처리할 기존 데이터가 포함되므로 이 기록 데이터를 소비하도록 작업을 구성해야 합니다.  

1.	페이지 위쪽에서 **구성**을 클릭합니다.
2.	**출력 시작** 값을 **사용자 지정 시간**으로 변경하고 시작 시간을 지정합니다.  시작 시간이 BasicEventHubSample을 실행한 시간보다 이전인지 확인합니다.  
3.	페이지 아래쪽에서 **저장**을 클릭한 후 **예**를 클릭하여 확인합니다.
3.	페이지 위쪽에서 **대시보드**를 클릭하고 페이지 아래쪽에서 **시작**을 클릭한 후 **예**를 클릭하여 확인합니다.  **간략 상태** 창에서 **상태**가 **시작 중**으로 변경되며, 시작 프로세스가 완료되어 **실행 중** 상태로 전환하는 데 몇 분 정도 걸릴 수 있습니다.   


##작업 출력 보기

1.	Visual Studio 또는 SQL Server Management Studio에서 SQL 데이터베이스에 연결하고 다음 쿼리를 실행합니다. 

		SELECT * FROM PassthroughReadings

2.	이벤트 허브에서 이벤트 읽기에 해당하는 레코드를 볼 수 있습니다.   

	![][img.stream.analytics.job.output1]

	BasicEventHubSample 응용 프로그램을 다시 실행하여 새 이벤트를 생성하거나 SELECT * 쿼리를 다시 실행하여 실시간으로 출력으로 전파되는 것을 확인할 수 있습니다.
	
	출력 누락 또는 예기치 않은 출력 문제가 발생하는 경우 대시보드 페이지의 오른쪽 창에 있는 연결된 작업에 대한 작업 로그를 확인하세요.

##작업 중지, 업데이트 및 다시 시작
이제 데이터에 대해 좀 더 흥미로운 쿼리를 수행해 보겠습니다.
1.	**대시보드** 또는 **모니터** 페이지에서 **중지**를 클릭합니다.
2.	**쿼리** 페이지에서 기존 쿼리를 다음으로 바꾸고 **저장**을 클릭합니다.

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	이 새 쿼리는 이벤트 허브에 푸시되는 시간을 타임스탬프로 사용하고 5초 간격의 평균 온도 판독값과 해당 5초 기간 내에 속하는 이벤트의 수를 찾아 예측합니다.
3.	**출력** 페이지에서 **편집**을 클릭합니다.  출력 테이블을 PassthroughReadings에서 AvgReadings로 변경하고 확인 아이콘을 클릭합니다.

4.	**대시보드** 페이지에서 **시작**을 클릭합니다.

##작업 출력 보기

1.	Visual Studio 또는 SQL Server Management Studio에서 SQL 데이터베이스에 연결하고 다음 쿼리를 실행합니다.

		SELECT * from AvgReadings

2.	각 장치에 대한 평균 온도와 이벤트의 수를 보여 주는 5 초 동안의 레코드를 확인합니다. 

	![][img.stream.analytics.job.output2]
 
3.	 실행 중인 작업에 의해 처리된 이벤트를 계속 보려면 BasicEventHubSample 응용 프로그램을 다시 실행합니다.







##<a name="nextsteps"></a>다음 단계
이 자습서에서는 스트림 분석을 사용하여 날씨 데이터를 처리하는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.


- [Azure 스트림 분석 소개][stream.analytics.introduction]
- [Azure 스트림 분석 개발자 가이드][stream.analytics.developer.guide]
- [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale]
- [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]
- [Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]
- [Azure 스트림 분석 관리 REST API 참조][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/ko-kr/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/ko-kr/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/ko-kr/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/ko-kr/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/ko-kr/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/ko-kr/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com

