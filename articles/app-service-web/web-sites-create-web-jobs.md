<properties 
	pageTitle="WebJob으로 백그라운드 작업 실행" 
	description="Azure 웹 앱에서 백그라운드 작업을 실행하는 방법에 대해 알아봅니다." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# WebJob으로 백그라운드 작업 실행

## 개요

[앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱에서 세 가지 방법, 요청 시, 계속 실행 또는 일정에 따라 웹 작업에서 프로그램이나 스크립트를 실행할 수 있습니다. 웹 작업을 사용하는 데 추가 비용은 없습니다.

이 문서에서는 [Azure 포털](https://portal.azure.com)에서 웹 작업을 배포하는 방법을 보여줍니다. Visual Studio 또는 지속적인 전송 프로세스를 사용하여 배포하는 방법에 대한 자세한 내용은 [Azure 웹 작업을 웹 앱에 배포하는 방법](websites-dotnet-deploy-webjobs.md)을 참조하세요.

Azure WebJobs SDK는 많은 웹 작업 프로그래밍 작업을 간소화합니다. 자세한 내용은 [WebJobs SDK 정의](websites-dotnet-webjobs-sdk.md)를 참조하세요.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="acceptablefiles"></a>스크립트 또는 프로그램에 허용 가능한 파일 형식

다음 파일 형식이 허용됩니다.

* .cmd, .bat, .exe(windows cmd 사용)
* .ps1(powershell 사용)
* .sh(bash 사용)
* .php(php 사용)
* .py(python 사용)
* .js(node 사용)
* .jar(java 사용)

## <a name="CreateOnDemand"></a>포털에서 요청 시 웹 작업 만들기

1. [Azure 포털](http://portal.azure.com)의 **웹 앱** 블레이드에서 **모든 설정 > 웹 작업**을 클릭하여 **웹 작업** 블레이드를 표시합니다.
	
	![WebJob 블레이드](./media/web-sites-create-web-jobs/wjblade.png)
	
5. **추가**를 클릭합니다. **웹 작업 추가** 대화 상자가 나타납니다.
	
	![WebJob 블레이드 추가](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. **이름**에 웹 작업의 이름을 입력합니다. 이름은 문자 또는 숫자로 시작해야 하며 "-" 및 "\_"을 제외한 다른 특수 문자를 포함할 수 없습니다.
	
4. **How to Run** 상자에서 **Run on Demand**를 선택합니다.
	
3. **파일 업로드** 상자에서 폴더 아이콘을 클릭하고 스크립트가 포함된 zip 파일을 찾습니다. zip 파일에는 실행 파일(.exe .cmd .bat .sh .php .py .js)과 더불어 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되어 있어야 합니다.
	
5. **만들기**를 선택하여 웹 앱에 스크립트를 업로드합니다.
	
	웹 작업에 지정된 이름은 **웹 작업** 블레이드의 목록에 표시됩니다.
	
6. 웹 작업을 실행하려면 목록에서 해당 이름을 마우스 오른쪽 단추로 클릭하고 **실행**을 클릭합니다.
	
	![WebJob 실행](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>지속적으로 실행 중인 웹 작업 만들기

1. 지속적으로 실행하는 웹 작업을 만들려면, 한 번 실행되는 웹 작업을 만드는 단계를 그대로 따르고 **실행 방법** 상자에서 **계속 실행**을 선택합니다.

2. 지속적인 웹 작업을 시작하거나 중지하려면 목록에서 웹 작업을 마우스 오른쪽 단추로 클릭하고 **시작** 또는 **중지**를 클릭합니다.
	
> [AZURE.NOTE]웹 앱이 둘 이상의 인스턴스에서 실행되는 경우 계속 실행되는 웹 작업은 모든 인스턴스에서 실행됩니다. 주문형 작업 및 예약형 작업은 Microsoft Azure의 부하 분산에 따라 선택된 단일 인스턴스에서 실행됩니다.
	
> 연속하는 WebJob을 모든 인스턴스에서 안정적으로 실행하려면 웹앱에 대한 항상 위* 구성 설정을 사용하도록 설정합니다. 그렇지 않으면 SCM 호스트 사이트를 오래 동안 사용하지 않을 경우 실행이 중지될 수 있습니다.

## <a name="CreateScheduledCRON"></a>CRON 식을 사용하여 예정된 WebJob 만들기

이 기술은 표준 또는 프리미엄 모드에서 실행 중인 웹앱에 사용할 수 있으며 앱에서 활성화하도록 **Always On** 설정이 필요합니다.

주문형 WebJob을 예정된 WebJob으로 설정하려면 WebJob zip 파일의 루트에 `settings.job` 파일을 포함하면 됩니다. 이 JSON 파일에는 아래 예제별로 [CRON 식](https://en.wikipedia.org/wiki/Cron)이 포함된 `schedule` 속성이 포함되어야 합니다.

CRON 식은 6개의 필드로 구성되어 있습니다. `{second} {minute} {hour} {day} {month} {day of the week}`.

예를 들어 15분마다 WebJob을 트리거하려면 `settings.job`에 다음 사항이 있어야 합니다.

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

다른 CRON 일정 예:

- 1시간마다(즉, 분을 나타내는 숫자가 0일 때마다): `0 0 * * * *` 
- 오전 9시에서 오후 5시까지 1시간마다: `0 0 9-17 * * *` 
- 매일 오전 9시 30분: `0 30 9 * * *`
- 평일 오전 9시 30분마다: `0 30 9 * * 1-5`

**참고**: Visual Studio에서 WebJob을 배포하는 경우 `settings.job` 파일 속성이 ‘변경된 내용만 복사'로 표시되는지 확인합니다.


## <a name="CreateScheduled"></a>Azure 스케줄러를 사용하여 예정된 WebJob 만들기

다음 대체 기술은 Azure 스케줄러를 사용합니다. 이 경우 WebJob에 일정의 직접적인 정보가 없습니다. 대신 Azure 스케줄러는 일정에서 WebJob을 트리거하도록 구성됩니다.

Azure 포털은 아직 예약된 웹 작업을 만들 수 없지만, [클래식 포털](http://manage.windowsazure.com)을 사용하여 기능이 추가될 때까지 예약된 웹 작업을 만들 수 있습니다.

1. [클래식 포털](http://manage.windowsazure.com)에서 웹 작업 페이지로 이동하고 **추가**를 클릭합니다.

1. **실행 방법** 상자에서 **일정에 따라 실행**을 선택합니다.
	
	![새 예약형 작업][NewScheduledJob]
	
2. 작업에 대해 **Scheduler Region**을 선택하고 대화 상자의 오른쪽 아래에 있는 화살표를 클릭하여 다음 화면으로 진행합니다.

3. **작업 만들기** 대화 상자에서 **일회성 작업** 또는 **작업 되풀이** 중 원하는 **되풀이** 유형을 선택합니다.
	
	![되풀이 예약][SchdRecurrence]
	
4. 또한 **지금** 또는 **특정 시간** 중에 **시작** 시간을 선택합니다.
	
	![시작 시간 예약][SchdStart]
	
5. 특정 시간에 시작하려면 **Starting On**에서 시작 시간 값을 선택합니다.
	
	![특정 시간에 시작 예약][SchdStartOn]
	
6. 되풀이 작업을 선택한 경우 발생 빈도를 지정하는 **Recur Every** 옵션과 종료 시간을 지정하는 **Ending On** 옵션이 나타납니다.
	
	![되풀이 예약][SchdRecurEvery]
	
7. **Weeks**를 선택하는 경우 **On a Particular Schedule** 상자를 선택하고 작업을 실행하려는 요일을 지정합니다.
	
	![요일 예약][SchdWeeksOnParticular]
	
8. **Months**를 선택하고 **On a Particular Schedule** 상자를 선택한 경우 한 달 중 특정 **Days**에 실행하도록 작업을 설정할 수 있습니다.
	
	![한 달 중 특정 날짜 예약][SchdMonthsOnPartDays]
	
9. **Week Days**를 선택하면 작업을 실행하려는 해당 달의 요일을 하나 이상 선택할 수 있습니다.
	
	![한 달 중 특정 요일 예약][SchdMonthsOnPartWeekDays]
	
10. 마지막으로 **Occurrences** 옵션을 사용하면 작업을 실행하도록 지정한 요일이 한 달 중 몇 번째 주(첫째, 둘째, 셋째 등)에 해당하는지를 선택할 수 있습니다.
	
	![한 달 중 특정 주의 특정 요일 예약][SchdMonthsOnPartWeekDaysOccurences]
	
11. 하나 이상의 작업을 만들면 WebJobs 탭에 작업의 상태, 일정 유형 및 기타 정보와 함께 이름이 나타납니다. 최근 30개의 웹 작업 기록 정보가 유지됩니다.
	
	![작업 목록][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>예약된 작업 및 Azure 스케줄러

[클래식 포털](http://manage.windowsazure.com)의 Azure 스케줄러 페이지에서 예약된 작업을 추가로 구성할 수 있습니다.

1.	Azure 스케줄러 포털 페이지로 이동하려면 WebJobs 페이지에서 작업의 **일정** 링크를 클릭합니다. 
	
	![Azure 스케줄러 링크][LinkToScheduler]
	
2. 스케줄러 페이지에서 작업을 클릭합니다.
	
	![스케줄러 포털 페이지의 작업][SchedulerPortal]
	
3. 작업을 추가로 구성할 수 있는 **Job Action** 페이지가 열립니다.
	
	![스케줄러의 작업 동작 페이지][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>작업 기록 보기

1. WebJobs SDK로 만든 작업을 포함하여 작업 실행 기록을 보려면 웹 작업 블레이드의 **로그** 열에서 해당 링크를 클릭합니다. 원하는 경우 클립보드 아이콘을 사용하여 로그 파일 페이지의 URL을 클립보드로 복사할 수 있습니다.
	
	![로그 링크](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. 링크를 클릭하면 웹 작업에 대한 세부 정보 페이지가 열립니다. 이 페이지에는 명령 실행 이름, 마지막으로 실행한 시간 및 명령 성공/실패 여부가 표시됩니다. **Recent job runs**에서 시간을 클릭하면 추가 세부 정보를 볼 수 있습니다.
	
	![WebJob 세부 정보][WebJobDetails]
	
3. **WebJob Run Details** 페이지가 나타납니다. 로그 내용의 텍스트를 보려면 **Toggle Output**을 클릭합니다. 출력 로그는 텍스트 형식으로 되어 있습니다.
	
	![WebJob 실행 세부 작업][WebJobRunDetails]
	
4. 별도의 브라우저 창에서 출력 텍스트를 보려면 **다운로드** 링크를 클릭합니다. 텍스트 자체를 다운로드하려면 링크를 마우스 오른쪽 단추로 클릭하고 브라우저 옵션을 사용하여 파일 내용을 저장합니다.
	
	![로그 출력 다운로드][DownloadLogOutput]
	
5. 페이지 맨 위의 **웹 작업** 링크를 사용하면 기록 대시보드의 웹 작업 목록을 편리하게 이용할 수 있습니다.
	
	![WebJobs 목록 링크][WebJobsLinkToDashboardList]
	
	![기록 대시보드의 작업 목록][WebJobsListInJobsDashboard]
	
	이러한 링크 중 하나를 클릭하면 선택한 작업의 WebJob Details 페이지로 이동합니다.


## <a name="WHPNotes"></a>참고 사항
	
- 무료 모드의 웹앱은 SCM(배포) 사이트에 대한 요청이 없는 경우 20분 후 시간 초과되고 웹앱의 포털이 Azure에서 열리지 않을 수 있습니다. 실제 사이트에 대한 요청이 있어도 이는 다시 설정되지 않습니다.
- 연속 작업을 위한 코드는 무한 반복으로 실행되도록 작성되어야 합니다.
- 연속 작업은 웹 앱이 실행되고 있는 경우에만 계속 실행됩니다.
- 기본 및 표준 모드에는 무중단 기능이 제공되며, 이 기능을 사용하도록 설정하면 웹 앱이 유휴 상태로 전환되지 않습니다.
- 계속 실행되는 웹 작업만을 디버깅할 수 있습니다. 예약된 또는 주문형 웹 작업 디버깅이 지원되지 않습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## <a name="NextSteps"></a>다음 단계
 
자세한 내용은 [Azure WebJob 권장 리소스][WebJobsRecommendedResources]를 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[PSonWebJobs]: http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]: http://go.microsoft.com/fwlink/?LinkId=390226

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
 

<!---HONumber=AcomDC_1203_2015-->