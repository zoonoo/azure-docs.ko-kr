<properties urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="WebJob을 사용하여 Microsoft Azure 웹 사이트에서 백그라운드 작업 실행" metaKeywords="Microsoft Azure 웹 사이트, WebJob, 백그라운드 작업" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="cephalin" />

#WebJob을 사용하여 Azure 웹 사이트에서 백그라운드 작업 실행

Azure 웹 사이트를 사용하면 사용자 웹 사이트에서 프로그램 또는 스크립트를 세 가지 방식, 즉 주문형, 지속형, 예약형 중 하나로 실행할 수 있습니다. Microsoft Azure WebJob을 사용하는 데는 별도의 비용이 들지 않습니다.

이 문서에서는 Azure 관리 포털을 사용하여 WebJob을 배포하는 방법을 보여 줍니다. Visual Studio 또는 지속적인 전송 프로세스를 사용하여 배포하는 방법에 대한 자세한 내용은 [Azure WebJob을 Azure 웹 사이트에 배포하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/websites-dotnet-deploy-webjobs)을 참조하세요.

Azure WebJobs SDK는 많은 WebJob 프로그래밍 작업을 간소화합니다. 자세한 내용은 [WebJobs SDK란?](../websites-dotnet-webjobs-sdk)을 참조하세요.

## 목차 ##
- [스크립트에 허용 가능한 파일 형식](#acceptablefiles)
- [주문형 작업 만들기](#CreateOnDemand)
- [연속형 작업 만들기](#CreateContinuous)
- [예약형 작업 만들기](#CreateScheduled)
	- [예약된 작업 및 Azure 스케줄러](#Scheduler)
- [작업 기록 보기](#ViewJobHistory)
- [참고 사항](#WHPNotes)
- [다음 단계](#NextSteps)

## <a name="acceptablefiles"></a>스크립트 또는 프로그램에 허용 가능한 파일 형식

다음 파일 형식이 허용됩니다.

* .cmd, .bat, .exe(windows cmd 사용)
* .ps1(powershell 사용)
* .sh(bash 사용)
* .php(php 사용)
* .py(python 사용)
* .js(node 사용)

## <a name="CreateOnDemand"></a>주문형 작업 만들기

1. **WebJob** 페이지의 명령 모음에서 **추가**를 클릭합니다. **새 작업** 대화 상자가 나타납니다.
	
	![On Demand Task][OnDemandWebJob]
	
2. **이름**에 작업 이름을 지정합니다. 이름은 문자 또는 숫자로 시작해야 하며 "-" 및 "_"을 제외한 다른 특수 문자를 포함할 수 없습니다.
	
3. **콘텐츠(zip 파일 - 최대 100Mb)** 상자에서 스크립트가 포함된 zip 파일로 이동합니다. zip 파일에는 실행 파일(.exe .cmd .bat .sh .php .py .js)과 더불어 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되어 있어야 합니다.
	
4. **실행 방법** 상자에서 **요청 시 실행**을 선택합니다.
	
5. 웹 사이트에 스크립트를 업로드하려면 대화 상자의 오른쪽 아래에 확인 표시가 있는지 확인합니다. 작업에 지정한 이름이 목록에 나타납니다.
	
	![Task List][WebJobsList]
	
6. 스크립트를 실행하려면 목록에서 이름을 선택하고 포털 페이지 아래의 명령 모음에서 **한 번 실행**을 클릭합니다.
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>연속형 작업 만들기

1. 연속형 작업을 만들려면 한 번 실행되는 작업을 만드는 단계를 그대로 따르고 **실행 방법** 상자에서 **계속 실행**을 선택합니다.
	
	![New Continuous Task][NewContinuousJob]
	
2. 연속형 작업을 시작 또는 중지하려면 목록에서 작업을 선택하고 명령 모음에서 **시작** 또는 **중지**를 클릭합니다.

> [WACOM.NOTE] 웹 사이트가 둘 이상의 인스턴스에서 실행되는 경우 연속형 작업은 모든 인스턴스에서 실행됩니다. 주문형 작업 및 예약형 작업은 Microsoft Azure의 부하 분산에 따라 선택된 단일 인스턴스에서 실행됩니다.

> [WACOM.NOTE]
> 연속형 작업의 경우 웹 사이트의 구성 페이지에서 **Always On**을 사용하도록 설정하는 것이 좋습니다. 기본 및 표준 모드에서 사용할 수 있는 Always On 기능은 웹 사이트가 일정 시간 동안 유휴 상태인 경우라도 언로드되는 것을 방지합니다. 웹 사이트가 항상 로드된 상태이면 연속형 작업은 좀 더 안정적으로 실행될 수 있습니다. 

## <a name="CreateScheduled"></a>예약형 작업 만들기

1. 예약형 작업을 만들려면 이전과 동일한 단계를 따르고 **실행 방법** 상자에서 **일정에 따라 실행**을 선택합니다.
	
	![New Scheduled Job][NewScheduledJob]
	
2. 작업에 대해 **스케줄러 지역**을 선택하고 대화 상자의 오른쪽 아래에 있는 화살표를 클릭하여 다음 화면으로 진행합니다.

3. **작업 만들기** 대화 상자에서 **되풀이** 유형으로 **일회성 작업** 또는 **되풀이 작업** 중 하나를 선택합니다.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. 또한 **시작** 시간으로 **지금** 또는 **특정 기간** 중에서 선택합니다.
	
	![Schedule Start Time][SchdStart]
	
5. 특정 시간에 시작하려면 **시작 지점**에서 시작 시간 값을 선택합니다.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. 되풀이 작업을 선택한 경우 발생 빈도를 지정하는 **되풀이 주기** 옵션과 종료 시간을 지정하는 **종료 지점** 옵션이 나타납니다.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. **주**를 선택하는 경우 **특정 일정에서** 상자를 선택하고 작업을 실행하려는 요일을 지정합니다.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. **월**을 선택하고 **특정 일정에서** 상자를 선택한 경우 한 달 중 특정 **일**에 실행하도록 작업을 설정할 수 있습니다. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. **요일**을 선택하면 작업을 실행하려는 해당 달의 요일을 하나 이상 선택할 수 있습니다.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. 마지막으로 **발생 빈도** 옵션을 사용하면 작업을 실행하도록 지정한 요일이 한 달 중 몇 번째 주(첫째, 둘째, 셋째 등)에 해당하는지를 선택할 수 있습니다.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. 하나 이상의 작업을 만들면 WebJob 탭에 작업의 상태, 일정 유형 및 기타 정보와 함께 이름이 나타납니다. 최근 30개 작업의 기록 정보가 유지됩니다.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>예약된 작업 및 Azure 스케줄러

예약형 작업을 Azure 스케줄러 포털에서 추가로 구성할 수 있습니다.

1.	Azure 스케줄러 포털 페이지로 이동하려면 WebJob 페이지에서 작업의 **일정** 링크를 클릭합니다. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. 스케줄러 페이지에서 작업을 클릭합니다.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. 작업을 추가로 구성할 수 있는 **작업 동작** 페이지가 열립니다. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>작업 기록 보기

1. WebJobs SDK로 만든 작업을 포함하여 작업 실행 기록을 보려면 **로그** 열에서 해당 링크를 클릭합니다. 원하는 경우 클립보드 아이콘을 사용하여 로그 파일 페이지의 URL을 클립보드로 복사할 수 있습니다.
	
	![Logs Link][WebJobLogs]
		
2. 링크를 클릭하면 작업에 대한 웹 작업 세부 정보 페이지가 열립니다. 이 페이지에는 명령 실행 이름, 마지막으로 실행한 시간 및 명령 성공/실패 여부가 표시됩니다. **Recent job runs**에서 시간을 클릭하면 추가 세부 정보를 볼 수 있습니다.
	
	![WebJobDetails][WebJobDetails]
	
3. **WebJob Run Details** 페이지가 나타납니다. 로그 내용의 텍스트를 보려면 **Toggle Output**을 클릭합니다. 출력 로그는 텍스트 형식으로 되어 있습니다. 
	
	![Web job run details][WebJobRunDetails]
	
4. 별도의 브라우저 창에서 출력 텍스트를 보려면 **다운로드** 링크를 클릭합니다. 텍스트 자체를 다운로드하려면 링크를 마우스 오른쪽 단추로 클릭하고 브라우저 옵션을 사용하여 파일 내용을 저장합니다.
	
	![Download log output][DownloadLogOutput]
	
5. 페이지 맨 위의 **WebJob** 링크를 사용하면 기록 대시보드의 웹 작업 목록을 편리하게 이용할 수 있습니다.
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	이러한 링크 중 하나를 클릭하면 선택한 작업의 WebJob Details 페이지로 이동합니다.


## <a name="WHPNotes"></a>참고 사항
	
- 2014년 3월부터 무료 모드의 웹 사이트는 SCM(배포) 사이트에 대한 요청이 없는 경우 20분 후 시간 초과되고 웹 사이트의 포털이 Azure에서 열리지 않을 수도 있습니다. 실제 사이트에 대한 요청이 있어도 이는 다시 설정되지 않습니다.
- 연속 작업을 위한 코드는 무한 반복으로 실행되도록 작성되어야 합니다.
- 연속 작업은 사이트가 실행되고 있는 경우에만 계속 실행됩니다.
- 기본 및 표준 모드에는 Always On 기능이 제공되며, 이 기능을 사용하도록 설정하면 사이트가 유휴 상태로 전환되는 것이 방지됩니다.

## <a name="NextSteps"></a>다음 단계
 
자세한 내용은 [Azure WebJob 권장 리소스][WebJobsRecommendedResources]를 참조하세요.

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
