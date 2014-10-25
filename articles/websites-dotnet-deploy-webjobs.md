<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Azure WebJob을 Azure 웹 사이트에 배포하는 방법

이 항목에서는 Visual Studio를 사용하여 콘솔 응용 프로그램 프로젝트를 Azure 웹 사이트에 [Azure WebJob][Azure WebJob]으로 배포하는 방법을 설명합니다. WebJob을 배포하는 또 다른 방법은 Azure 관리 포털을 사용하는 것입니다. 해당 방법에 대한 자세한 내용은 [Microsoft Azure 웹 사이트에서 WebJob을 사용하여 백그라운드 작업 실행][Microsoft Azure 웹 사이트에서 WebJob을 사용하여 백그라운드 작업 실행]을 참조하세요.

## 목차

-   [개요][개요]
-   [필수 조건][필수 조건]
-   [기존 콘솔 응용 프로그램 프로젝트에 WebJob 배포 사용][기존 콘솔 응용 프로그램 프로젝트에 WebJob 배포 사용]
-   [새 WebJob 지원 프로젝트 만들기][새 WebJob 지원 프로젝트 만들기]
-   [Add Azure WebJob(Azure WebJob 추가) 대화 상자][Add Azure WebJob(Azure WebJob 추가) 대화 상자]
-   [webjob-publish-settings.json 파일][webjob-publish-settings.json 파일]
-   [webjobs-list.json 파일][webjobs-list.json 파일]
-   [WebJob 프로젝트 배포][WebJob 프로젝트 배포]
-   [다음 단계][다음 단계]

## <span id="overview"></span></a>개요

Visual Studio는 WebJob 지원 콘솔 응용 프로그램 프로젝트를 배포할 때 다음 두 가지 작업을 수행합니다.

-   Azure 웹 사이트의 해당 폴더에 런타임 파일을 복사합니다(연속 WebJob의 경우 *App\_Data/jobs/continuous*, 예약된 주문형 WebJob의 경우 *App\_Data/jobs/triggered*).
-   특정 시간에 실행되도록 예약된 WebJob에 대해 [Azure 스케줄러 작업][Azure 스케줄러 작업]을 설정합니다. (연속 WebJob에는 필요하지 않습니다.)

WebJob 지원 프로젝트에는 다음 항목이 추가됩니다.

-   [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish] NuGet 패키지
-   배포 및 스케줄러 설정을 포함하는 [webjob-publish-settings.json][webjob-publish-settings.json 파일] 파일

![배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램][배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램]

이러한 항목을 기존 콘솔 응용 프로그램 프로젝트에 추가하거나 템플릿을 사용하여 새 WebJob 지원 콘솔 응용 프로그램 프로젝트를 만들 수 있습니다.

프로젝트를 WebJob 자체로 배포하거나 웹 프로젝트를 배포할 때마다 자동으로 배포되도록 웹 프로젝트에 연결할 수 있습니다. 프로젝트를 연결할 수 있게 Visual Studio는 웹 프로젝트의 [webjobs-list.json][webjobs-list.json 파일] 파일에 WebJob 지원 프로젝트의 이름을 포함합니다.

![웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램][웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램]

## 필수 조건

WebJob 배포 기능은 Azure SDK 버전 2.4 이상을 설치할 때 Visual Studio 2013에서 사용할 수 있습니다.

-   [Visual Studio 2013용 Azure SDK][Visual Studio 2013용 Azure SDK](영문)

WebJob 배포 기능은 [업데이트 3][업데이트 3]에도 포함되어 있습니다.

## <span id="convert"></span></a>기존 콘솔 응용 프로그램 프로젝트에 WebJob 배포 사용

다음 두 가지 옵션을 사용할 수 있습니다.

-   [웹 프로젝트를 사용하여 자동 배포 사용][웹 프로젝트를 사용하여 자동 배포 사용].

    기존 콘솔 응용 프로그램 프로젝트가 웹 프로젝트를 배포할 때 WebJob으로 자동 배포되도록 구성합니다. 관련 웹 응용 프로그램을 실행하는 동일한 웹 사이트에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

-   [웹 프로젝트를 제외하고 배포][웹 프로젝트를 제외하고 배포].

    기존 콘솔 응용 프로그램 프로젝트가 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 사이트에서 웹 응용 프로그램은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.

### <span id="convertlink"></span></a> 웹 프로젝트와 함께 자동 WebJob 배포

1.  **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** \> **Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)**을 클릭합니다.

    ![Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)][Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)]

    [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자가 나타납니다.

2.  **프로젝트 이름** 드롭다운 목록에서 WebJob으로 추가할 콘솔 응용 프로그램 프로젝트를 선택합니다.

    ![Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택][Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택]

3.  [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자를 완료하고 **확인**을 클릭합니다.

### <span id="convertnolink"></span></a> 웹 프로젝트를 제외하고 WebJob 배포

1.  **솔루션 탐색기**에서 콘솔 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Publish as Azure WebJob(Azure WebJob으로 게시)**을 클릭합니다.

    ![Publish as Azure WebJob(Azure WebJob으로 게시)][Publish as Azure WebJob(Azure WebJob으로 게시)]

    **프로젝트 이름** 상자에서 프로젝트가 선택된 상태로 [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자가 나타납니다.

2.  [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자를 완료하고 **확인**을 클릭합니다.

    **웹 게시** 마법사가 나타납니다. 바로 게시하지 않으려면 마법사를 닫습니다. 입력한 설정은 [프로젝트를 배포][WebJob 프로젝트 배포]할 때 사용할 수 있게 저장됩니다.

## <span id="create"></span></a>새 WebJob 지원 프로젝트 만들기

새 WebJob 지원 프로젝트를 만들려면 [이전 섹션][기존 콘솔 응용 프로그램 프로젝트에 WebJob 배포 사용]에 설명된 대로 콘솔 응용 프로그램 프로젝트 템플릿을 사용하고 WebJob 배포를 사용하도록 설정할 수 있습니다. 또는 WebJob new-project 템플릿을 사용할 수도 있습니다.

-   [웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용][웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용]

    동일한 솔루션의 웹 프로젝트가 배포될 때 자동으로 WebJob으로 배포되도록 구성된 프로젝트를 만듭니다. 관련 웹 응용 프로그램을 실행하는 동일한 웹 사이트에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

-   [독립 WebJob을 위해 WebJob new-project 템플릿 사용][독립 WebJob을 위해 WebJob new-project 템플릿 사용]

    프로젝트를 만든 다음 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 사이트에서 웹 응용 프로그램은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 응용 프로그램 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.

SDK 2.4 릴리스에서 WebJob new-project 템플릿은 콘솔 응용 프로그램 프로젝트를 만들고 WebJob 배포를 사용하도록 설정하는 것보다 그렇게 쉽지 않습니다. 앞으로는 해당 WebJob SDK NuGet 패키지가 자동으로 설치되므로 [WebJob SDK][WebJob SDK] 개발 시 WebJob new-project 템플릿을 좀 더 편리하게 사용할 수 있게 될 것입니다. 그때까지는 [WebJob SDK 자습서][WebJob SDK]에 나오는 것처럼 패키지를 수동으로 설치하여 WebJob SDK를 사용하도록 프로젝트를 구성할 수 있습니다.

### <span id="createlink"></span></a> 웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용

-   **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** \> **New Azure WebJob Project(새 Azure WebJob 프로젝트)**를 클릭합니다.

    ![New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목][New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목]

    [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자가 나타납니다.

-   [Add Azure WebJob(Azure WebJob 추가)][Add Azure WebJob(Azure WebJob 추가) 대화 상자] 대화 상자를 완료하고 **확인**을 클릭합니다.

### <span id="createnolink"></span></a> 독립 WebJob을 위해 WebJob new-project 템플릿 사용

1.  **파일** \> **새 프로젝트**를 클릭한 후 **새 프로젝트** 대화 상자에서 **클라우드** \> **Microsoft Azure WebJob**을 클릭합니다.

    ![WebJob 템플릿을 표시하는 새 프로젝트 대화 상자][WebJob 템플릿을 표시하는 새 프로젝트 대화 상자]

2.  앞에 표시된 지침에 따라 [콘솔 응용 프로그램 프로젝트를 독립 WebJob 프로젝트로 만듭니다][웹 프로젝트를 제외하고 배포].

## <span id="configure"></span></a>Add Azure WebJob(Azure WebJob 추가) 대화 상자

**Add Azure WebJob(Azure WebJob 추가)** 대화 상자에서 WebJob 이름과 WebJob에 대한 일정 설정을 입력할 수 있습니다.

![Add Azure WebJob(Azure WebJob 추가) 대화 상자][1]

이 대화 상자의 필드는 Azure 관리 포털의 **새 작업** 대화 상자에 있는 필드에 해당합니다. 자세한 내용은 [Microsoft Azure 웹 사이트에서 WebJob을 사용하여 백그라운드 작업 실행][Microsoft Azure 웹 사이트에서 WebJob을 사용하여 백그라운드 작업 실행]을 참조하세요.

예약된 WebJob(연속 WebJob 아님)의 경우 Visual Studio는 [Azure 스케줄러][Azure 스케줄러] 작업 컬렉션이 아직 없으면 만들고 이 컬렉션에 작업을 만듭니다.

-   스케줄러 작업 컬렉션 이름은 *WebJobs-{regionname}*입니다. 여기서 *{regionname}*은 웹 사이트가 호스트되는 지역입니다. 예를 들면 다음과 같습니다. WebJobs-WestUS.
-   스케줄러 작업 이름은 *{websitename}-{webjobname}*입니다. 예를 들면 다음과 같습니다. MyWebSite-MyWebJob.

> [WACOM.NOTE]
>
> -   MSBuild를 사용하는 명령줄 배포는 예약된 WebJob에 대한 일정을 설정하지 않습니다. 이 기능은 Visual Studio를 사용하여 배포할 때만 작동합니다.
> -   **되풀이 작업**을 구성하고 되풀이 빈도를 분 단위로 설정하는 경우에는 Azure 스케줄러 서비스가 무료가 아닙니다. 다른 빈도(시간, 일 등)로 설정하는 것은 무료입니다.
> -   WebJob을 배포하고 나중에 실행 모드를 연속에서 비연속으로 또는 그 반대로 변경하면 Visual Studio는 사용자가 WebJob을 다시 배포할 때 Azure에서 새 WebJob을 만듭니다. 다른 일정 설정을 변경하고 실행 모드를 그대로 두거나 예약형 및 주문형 간을 전환하면 Visual Studio는 새 작업을 만들지 않고 기존 작업을 업데이트합니다.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

WebJob 배포를 위해 콘솔 응용 프로그램을 구성하는 경우 Visual Studio는 [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish] NuGet 패키지를 설치하고
WebJob 프로젝트의 프로젝트 *Properties* 폴더에 있는 *webjob-publish-settings.json* 파일에 일정 정보를 저장합니다. 다음은 이 파일의 예입니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [][]<http://schemastore.org></a>에 저장되고 이 위치에서 볼 수 있습니다.

> [WACOM.NOTE]
>
> -   **되풀이 작업**을 구성하고 되풀이 빈도를 분 단위로 설정하는 경우에는 Azure 스케줄러 서비스가 무료가 아닙니다. 다른 빈도(시간, 일 등)로 설정하는 것은 무료입니다.

## <span id="webjobslist"></span></a>webjobs-list.json

WebJob 지원 프로젝트를 웹 프로젝트에 연결하면 Visual Studio는 WebJob 프로젝트의 이름을 웹 프로젝트의 *Properties* 폴더에 있는 *webjobs-list.json* 파일에 저장합니다. 이 목록에는 다음 예와 같이 여러 WebJob 프로젝트가 포함될 수 있습니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마는 [][2]<http://schemastore.org></a>에 저장되고 이 위치에서 볼 수 있습니다.

## <span id="deploy"></span></a>WebJob 프로젝트 배포

웹 프로젝트에 연결한 WebJob 프로젝트는 웹 프로젝트와 함께 자동으로 배포됩니다. 웹 프로젝트 배포에 대한 자세한 내용은 [Azure 웹 사이트 배포 방법][Azure 웹 사이트 배포 방법]을 참조하세요.

WebJob 프로젝트 자체를 배포하려면 **솔루션 탐색기**에서 이 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Publish as Azure WebJob(Azure WebJob으로 게시)**을 클릭합니다.

![Publish as Azure WebJob(Azure WebJob으로 게시)][Publish as Azure WebJob(Azure WebJob으로 게시)]

독립 WebJob의 경우 웹 프로젝트에 사용되는 것과 동일한 **웹 게시** 마법사가 나타나지만 변경할 수 있는 설정은 더 적습니다.

## <span id="nextsteps"></span></a>다음 단계

Azure WebJob 및 WebJob SDK에 대한 자세한 내용은 [Azure WebJob - 권장 리소스][Azure WebJob - 권장 리소스](영문)를 참조하세요.

  [Azure WebJob]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Microsoft Azure 웹 사이트에서 WebJob을 사용하여 백그라운드 작업 실행]: /ko-kr/documentation/articles/web-sites-create-web-jobs/
  [개요]: #overview
  [필수 조건]: #prerequisites
  [기존 콘솔 응용 프로그램 프로젝트에 WebJob 배포 사용]: #convert
  [새 WebJob 지원 프로젝트 만들기]: #create
  [Add Azure WebJob(Azure WebJob 추가) 대화 상자]: #configure
  [webjob-publish-settings.json 파일]: #publishsettings
  [webjobs-list.json 파일]: #webjobslist
  [WebJob 프로젝트 배포]: #deploy
  [다음 단계]: #next-steps
  [Azure 스케줄러 작업]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램]: ./media/websites-dotnet-deploy-webjobs/link.png
  [Visual Studio 2013용 Azure SDK]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [업데이트 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [웹 프로젝트를 사용하여 자동 배포 사용]: #convertlink
  [웹 프로젝트를 제외하고 배포]: #convertnolink
  [Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Publish as Azure WebJob(Azure WebJob으로 게시)]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용]: #createlink
  [독립 WebJob을 위해 WebJob new-project 템플릿 사용]: #createnolink
  [WebJob SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [WebJob 템플릿을 표시하는 새 프로젝트 대화 상자]: ./media/websites-dotnet-deploy-webjobs/np.png
  [1]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Azure 스케줄러]: /en-us/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [2]: http://schemastore.org/schemas/json/webjobs-list.json
  [Azure 웹 사이트 배포 방법]: /ko-kr/documentation/articles/websites-dotnet-deploy/
  [Azure WebJob - 권장 리소스]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
