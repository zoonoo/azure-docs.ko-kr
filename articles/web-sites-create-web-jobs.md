<properties  linkid="web-sites-create-web-jobs" urlDisplayName="How to create web jobs in Microsoft Azure" pageTitle="How to Create Web Jobs in Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Web Jobs" description="Learn how to create web jobs in Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create Web Jobs in Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

# Microsoft Azure 웹 사이트에서 WebJob 기능 사용 방법

Microsoft Azure 웹 사이트를 사용하면 사용자 웹 사이트에서 프로그램 또는 스크립트를 세 가지 방식, 즉 주문형, 지속형, 예약형 중 하나로 실행할 수 있습니다. 이 문서의 뒷부분에서 설명하는 Always On 기능을 사용하는 경우가 아니라면 Microsoft Azure WebJob을 사용하는 데는 별도의 비용이 들지 않습니다.

## 목차

* [스크립트에 허용 가능한 파일 형식](#acceptablefiles)
* [주문형 작업 만들기](#CreateOnDemand)
* [연속형 작업 만들기](#CreateContinuous)
* [예약형 작업 만들기](#CreateScheduled)
  * [예약된 작업 및 Azure 스케줄러](#Scheduler)

* [작업 기록 보기](#ViewJobHistory)
* [참고 사항](#WHPNotes)
* [다음 단계](#NextSteps)
  * [Microsoft Azure WebJobs SDK의 추가 기능](#WebJobsSDK)
  * [대체 배포 방법](#AlternateDeployments)
  * [추가 리소스](#AdditionalResources)

## 스크립트에 허용 가능한 파일 형식##
실행 가능한 스크립트로 허용되는 파일 형식은 다음과 같습니다.

.cmd, .bat, .exe(windows cmd 사용)

.ps1(powershell 사용)

.sh(bash 사용)

.php(php 사용)

.py(python 사용)

.js(node 사용)

## 주문형 작업 만들기##

1.  **WebJobs** 페이지의 명령 모음에서 **추가**를 클릭합니다. **새 작업** 대화 상자가 나타납니다.
    
    ![주문형 작업](./media/web-sites-create-web-jobs/01aOnDemandWebJob.png)

2.  **이름**에 작업 이름을 지정합니다. 이름은 문자 또는 숫자로 시작해야 하며 "-" 및 "\_"을 제외한 다른 특수 문자를 포함할 수 없습니다.

3.  **Content (Zip Files - 100MB Max)** 상자에서 스크립트가 포함된 zip 파일로 이동합니다. zip 파일에는 실행 파일(.exe .cmd .bat .sh .php .py .js)과 더불어 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되어 있어야 합니다.

4.  **How to Run** 상자에서 **Run on Demand**를 선택합니다.

5.  웹 사이트에 스크립트를 업로드하려면 대화 상자의 오른쪽 아래에 확인 표시가 있는지 확인합니다. 작업에 지정한 이름이 목록에 나타납니다.
    
    ![작업 목록](./media/web-sites-create-web-jobs/02aWebJobsList.png)

6.  스크립트를 실행하려면 목록에서 이름을 선택하고 포털 페이지 아래의 명령 모음에서 **Run Once**를 클릭합니다.
    
    ![Run Once](./media/web-sites-create-web-jobs/13RunOnce.png)

## 연속형 작업 만들기##

1.  연속형 작업을 만들려면 한 번 실행되는 작업을 만드는 단계를 그대로 따르고 **How to Run** 상자에서 **Run continuously**를 선택합니다.
    
    ![새로운 연속형 작업](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/03anewcontinuousjob.png)

2.  연속형 작업을 시작 또는 중지하려면 목록에서 작업을 선택하고 명령 모음에서 **시작** 또는 **중지**를 클릭합니다.

> [WACOM.NOTE] 웹 사이트가 둘 이상의 인스턴스에서 실행되는 경우 연속형 작업은 모든 인스턴스에서 실행됩니다.
> 주문형 작업 및 예약형 작업은 Microsoft Azure의 부하 분산에 따라 선택된 단일 인스턴스에서 실행됩니다.

> [WACOM.NOTE] 연속형 작업의 경우 웹 사이트의 구성 페이지에서 **Always On**을 사용하도록 설정하는 것이
> 좋습니다. 기본 및 표준 모드에서 사용할 수 있는 Always On 기능은 웹 사이트가 일정 시간 동안 유휴 상태인 경우라도
> 언로드되는 것을 방지합니다. 웹 사이트가 항상 로드된 상태이면 연속형 작업은 좀 더 안정적으로 실행될 수 있습니다.

## 예약형 작업 만들기

1. 예약형 작업을 만들려면 이전과 동일한 단계를 따르고 **How to Run** 상자에서 **Run on a schedule**을 선택합니다.

	![NewScheduledJob][NewScheduledJob]

1.  작업에 대해 **Scheduler Region**을 선택하고 대화 상자의 오른쪽 아래에 있는 화살표를 클릭하여 다음 화면으로 진행합니다.

2.  **Create Job** 대화 상자에서 **Recurrence** 유형으로 **One-time job** 또는 **Recurring job** 중 하나를 선택합니다.
    
    ![되풀이 예약](./media/web-sites-create-web-jobs/05SchdRecurrence.png)

3.  또한 **Starting** 시간으로 **Now** 또는 **At a specific time** 중에서 선택합니다.
    
    ![시작 시간 예약](./media/web-sites-create-web-jobs/06SchdStart.png)

4.  특정 시간에 시작하려면 **Starting On**에서 시작 시간 값을 선택합니다.
    
    ![특정 시간에 시작 예약](./media/web-sites-create-web-jobs/07SchdStartOn.png)

5.  되풀이 작업을 선택한 경우 발생 빈도를 지정하는 **Recur Every** 옵션과 종료 시간을 지정하는 **Ending On** 옵션이 나타납니다.
    
    ![되풀이 예약](./media/web-sites-create-web-jobs/08SchdRecurEvery.png)

6.  **Weeks**를 선택하는 경우 **On a Particular Schedule** 상자를 선택하고 작업을 실행하려는 요일을 지정합니다.
    
    ![요일 예약](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/09schdweeksonparticular.png)

7.  **Months**를 선택하고 **On a Particular Schedule** 상자를 선택한 경우 한 달 중 특정 **Days**에 실행하도록 작업을 설정할 수 있습니다.
    
    ![한 달 중 특정 날짜 예약](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/10schdmonthsonpartdays.png)

8.  **Week Days**를 선택하면 작업을 실행하려는 해당 달의 요일을 하나 이상 선택할 수 있습니다.
    
    ![한 달 중 특정 요일 예약](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/11schdmonthsonpartweekdays.png)

9.  마지막으로 **Occurrences** 옵션을 사용하면 작업을 실행하도록 지정한 요일이 한 달 중 몇 번째 주(첫째, 둘째, 셋째 등)에 해당하는지를 선택할 수 있습니다.
    
    ![한 달 중 특정 주의 특정 요일 예약](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/12schdmonthsonpartweekdaysoccurences.png)

10. 하나 이상의 작업을 만들면 WebJobs 탭에 작업의 상태, 일정 유형 및 기타 정보와 함께 이름이 나타납니다. 최근 30개 작업의 기록 정보가 유지됩니다.
    
    ![작업 목록](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/13webjobslistwithseveraljobs.png)

### 예약된 작업 및 Azure 스케줄러 예약 작업을
Azure 스케줄러 포털에서 추가로 구성할 수 있습니다.

1.  Azure 스케줄러 포털 페이지로 이동하려면 WebJobs 페이지에서 작업의 **일정** 링크를 클릭합니다.
    
    ![Azure 스케줄러 링크](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/31linktoscheduler.png)

2.  스케줄러 페이지에서 작업을 클릭합니다.
    
    ![스케줄러 포털 페이지의 작업](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/32schedulerportal.png)

3.  작업을 추가로 구성할 수 있는 **Job Action** 페이지가 열립니다.
    
    ![스케줄러의 작업 동작 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/33jobactionpageinscheduler.png)

<!-- ================ ViewJobHistory ================= -->

## 작업 기록 보기
1. WebJobs SDK로 만든 작업을 포함하여 작업 실행 기록을 보려면 **Logs** 열에서 해당 링크를 클릭합니다. 원하는 경우 클립보드 아이콘을 사용하여 로그 파일 페이지의 URL을 클립보드로 복사할 수 있습니다.

	![Logs Link][WebJobLogs]

1.  링크를 클릭하면 작업에 대한 WebJob 세부 정보 페이지가 열립니다. 이 페이지에는 명령 실행 이름, 마지막으로 실행한 시간 및 명령 성공/실패 여부가 표시됩니다. **Recent job runs**에서 시간을 클릭하면 추가 세부 정보를 볼 수 있습니다.
    
    ![WebJob 세부 정보](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/15webjobdetails.png)

2.  **WebJob Run Details** 페이지가 나타납니다. 로그 내용의 텍스트를 보려면 **Toggle Output**을 클릭합니다. 출력 로그는 텍스트 형식으로 되어 있습니다.
    
    ![WebJob 실행 세부 작업](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/16webjobrundetails.png)

3.  별도의 브라우저 창에서 출력 텍스트를 보려면 **다운로드** 링크를 클릭합니다. 텍스트 자체를 다운로드하려면 링크를 마우스   오른쪽 단추로 클릭하고 브라우저 옵션을 사용하여 파일 내용을 저장합니다.
    
    ![로그 출력 다운로드](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/17downloadlogoutput.png)

4.  페이지 맨 위의 **WebJobs** 링크를 사용하면 기록 대시보드의 웹 작업 목록을 편리하게 이용할 수 있습니다.
    
    ![웹 작업 목록 링크](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/18webjobslinktodashboardlist.png)
    
    ![기록 대시보드의 작업 목록](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/ko-kr/documentation/articles/web-sites-create-web-jobs/20140815090453/19webjobslistinjobsdashboard.png)
    
    이러한 링크 중 하나를 클릭하면 선택한 작업의 WebJob Details 페이지로 이동합니다.

<a name="WHPNotes"></a> 

## 참고 사항

* 2014년 3월부터 무료 모드의 웹 사이트는 SCM(배포) 사이트에 대한 요청이 없는 경우 20분 후 시간 초과되고 웹
  사이트의 포털이 Azure에서 열리지 않을 수도 있습니다. 실제 사이트에 대한 요청이 있어도 이는 다시 설정되지 않습니다.

* 연속 작업을 위한 코드는 무한 반복으로 실행되도록 작성되어야 합니다.

* 연속 작업은 사이트가 실행되고 있는 경우에만 계속 실행됩니다.

* 기본 및 표준 모드에는 Always On 기능이 제공되며, 이 기능을 사용하도록 설정하면 사이트가 유휴 상태로 전환되는 것이
  방지됩니다.

## 다음 단계

### Microsoft Azure WebJobsSDK의 추가 기능 
Microsoft Azure WebJobs SDK를 사용하면 Microsoft Azure 웹 사이트에 후순위 처리를 추가하는 작업이 간소화됩니다. SDK에는 Microsoft Azure 저장소가 통합되어 있으며, SDK는 큐, Blob 또는 테이블에 항목이 추가될 때 프로그램에서 기능을 트리거합니다. 이제 Azure 포털에 통합된 대시보드는 SDK를 사용하여 작성한 프로그램에 대해 풍부한 모니터링 및 진단 기능을 제공합니다. 모니터링 및 진단 기능은 SDK에 빌드되어 있으므로 프로그램에 특별 코드를 추가할 필요가 없습니다.

자세한 내용은 [Microsoft Azure WebJobs SDK 시작][1](영문) 자습서를 참조하십시오. 자습서에는 WebJobs SDK 기능이 개략적으로 설명되어 있으며 간단한 Hello World 후순위 처리 작업을 만들고 실행하는 단계가 안내되어 있습니다.

Microsoft Azure WebJobs SDK로 만든 샘플 명령줄 앱의 단계별 절차를 확인하려면 [Azure WebJobs 소개][2](영문)를 참조하십시오.

<a name="AlternateDeployments"></a> 

### 대체 배포 방법
스크립트를 업로드하는 데 WebJobs 포털 페이지를 사용하지 않으려면 FTP, git 또는 웹 배포를 사용할 수 있습니다.
자세한 내용은 [Azure WebJob 배포 방법][3](영문) 및 [WebJob을 사용하여 .NET 콘솔 앱을 Azure에 배포하는 git][4](영문)를 참조하십시오.

<a name="AdditionalResources"></a> 

### 추가 리소스
WebJob 기능에 대해 주석이 첨부된 링크 목록을 확인하려면 [Azure 웹 사이트의 WebJob 기능 사용][5](영문)을 참조하십시오.

* WebJob 관련 비디오:
  
  [Azure WebJobs 101 - Jamie Espinosa와 함께 하는 WebJob 기초(영문)][6]
  
  [Azure WebJobs 102 - Jamie Espinosa가 설명하는 예약된 WebJob 및 WebJob
  대시보드(영문)][7]
  
  [Azure Scheduler 101 - Kevin Lam이 설명하는 작업 예약 방법(영문)][8]

<!-- LINKS -->

<!-- IMAGES -->

[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png

[1]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[2]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[3]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
[4]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
[5]: http://go.microsoft.com/fwlink/?LinkId=390226
[6]: http://www.windowsazure.com/ko-kr/documentation/videos/azure-webjobs-basics/
[7]: http://www.windowsazure.com/ko-kr/documentation/videos/azure-webjobs-schedule-and-dashboard/
[8]: http://www.windowsazure.com/ko-kr/documentation/videos/azure-scheduler-how-to/