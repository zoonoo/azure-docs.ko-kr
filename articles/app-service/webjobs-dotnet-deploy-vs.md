---
title: VS를 사용하여 웹 작업 개발 및 배포
description: Visual Studio에서 Azure WebJobs를 개발하고 예약된 작업 만들기를 포함하여 Azure 앱 서비스에 배포하는 방법을 알아봅니다.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: feacd463a10bae66dc8fa88a99b9ea60f399e9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684163"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure App Service

이 문서에서는 Visual Studio를 사용하여 콘솔 응용 프로그램 프로젝트를 [Azure WebJob로](https://go.microsoft.com/fwlink/?LinkId=390226) [앱 서비스의](overview.md) 웹 앱에 배포하는 방법을 설명합니다. [Azure Portal](https://portal.azure.com)을 사용하여 WebJobs를 배포하는 방법에 대한 내용은 [WebJobs로 백그라운드 작업 실행](webjobs-create.md)을 참조하세요.

여러 WebJobs를 단일 웹 앱에 게시할 수 있습니다. 웹 앱의 각 WebJob에 고유한 이름이 있는지 확인합니다.

[Azure WebJobs SDK의](webjobs-sdk-how-to.md) 버전 3.x를 사용하면 .NET Core 앱 또는 .NET Framework 앱으로 실행되는 WebJobs를 개발할 수 있으며 버전 2.x는 .NET 프레임워크만 지원합니다. WebJobs 프로젝트를 배포하는 방법은 .NET Core 프로젝트와 .NET Framework 프로젝트에 대해 다릅니다.

## <a name="webjobs-as-net-core-console-apps"></a>.NET 코어 콘솔 앱으로 웹잡스

WebJobs의 버전 3.x를 사용하는 경우 WebJobs를 만들고 게시할 수 있습니다. .NET Core 콘솔 응용 프로그램을 만들고 게시하는 단계별 지침은 이벤트 기반 [백그라운드 처리를 위해 Azure WebJobs SDK로 시작하기를](webjobs-sdk-get-started.md)참조하십시오.

> [!NOTE]
> .NET 코어 웹작업은 웹 프로젝트와 연결할 수 없습니다. 웹 앱으로 WebJob을 배포해야 하는 경우 [.NET Framework 콘솔 앱으로 WebJob을 만들어야 합니다.](#webjobs-as-net-framework-console-apps)  

### <a name="deploy-to-azure-app-service"></a>Azure App Service에 배포

Visual Studio에서 앱 서비스에 .NET Core WebJob을 게시하는 것은 ASP.NET 핵심 앱을 게시하는 것과 동일한 도구를 사용합니다.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob 형식

기본적으로 .NET Core 콘솔 프로젝트에서 게시된 WebJob은 트리거되거나 요청 시만 실행됩니다. [일정에서 실행하거나](#scheduled-execution) 지속적으로 실행하도록 프로젝트를 업데이트할 수도 있습니다.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>예약된 실행

Azure에 .NET Core 콘솔 응용 프로그램을 게시하면 새 *settings.job* 파일이 프로젝트에 추가됩니다. 이 파일을 사용하여 WebJob에 대한 실행 일정을 설정합니다. 자세한 내용은 [트리거된 WebJob 예약을](#scheduling-a-triggered-webjob)참조하십시오.

#### <a name="continuous-execution"></a>연속 실행

Visual Studio를 사용하여 Azure에서 Always On이 활성화되어 있을 때 WebJob을 계속 실행하도록 변경할 수 있습니다.

1. 아직 수행하지 않은 경우 프로젝트를 Azure 에 [게시합니다.](#deploy-to-azure-app-service)

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시** 탭에서 **설정을**선택합니다. 

1. 프로필 **설정** 대화 상자에서 **WebJob 유형에**대해 **연속을** 선택하고 **저장을**선택합니다.

    ![WebJob에 대한 설정 대화 상자 게시](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 업데이트된 설정으로 WebJob을 다시 게시하려면 **게시를** 선택합니다.

## <a name="webjobs-as-net-framework-console-apps"></a>.NET 프레임워크 콘솔 앱으로 웹채용  

Visual Studio에서 WebJobs 지원 .NET Framework 콘솔 응용 프로그램 프로젝트를 배포할 때 런타임 파일을 웹 앱의 해당 폴더에 복사합니다(연속 WebJobs및 예약된 웹잡에 대해 *트리거된 App_Data/작업/트리거된* 웹 앱의 경우*App_Data/작업/연속).*

WebJob 지원 프로젝트에는 다음 항목이 추가됩니다.

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지
* 배포 및 스케줄러 설정을 포함하는 [webjob-publish-settings.json](#publishsettings) 파일 

![배포를 WebJob으로 설정하기 위해 콘솔 앱에 추가되는 내용을 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/convert.png)

이러한 항목을 기존 콘솔 애플리케이션 프로젝트에 추가하거나 템플릿을 사용하여 새 WebJob 지원 콘솔 애플리케이션 프로젝트를 만들 수 있습니다. 

프로젝트를 WebJob 자체로 배포하거나 웹 프로젝트를 배포할 때마다 자동으로 배포되도록 웹 프로젝트에 연결할 수 있습니다. 프로젝트를 연결할 수 있게 Visual Studio는 웹 프로젝트의 [webjobs-list.json](#webjobslist) 파일에 WebJob 지원 프로젝트의 이름을 포함합니다.

![웹 프로젝트에 연결된 WebJob 프로젝트를 보여 주는 다이어그램](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>사전 요구 사항

Visual Studio 2015를 사용하는 경우 [Azure SDK for .NET(Visual Studio 2015)](https://azure.microsoft.com/downloads/)을 설치합니다.

Visual Studio 2017을 사용하는 경우 [Azure 개발 워크로드](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)를 설치합니다.

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>기존 콘솔 응용 프로그램 프로젝트에 대한 WebJobs 배포 사용

다음 두 가지 옵션을 사용할 수 있습니다.

* [웹 프로젝트를 사용하여 자동 배포 사용](#convertlink).

  기존 콘솔 애플리케이션 프로젝트가 웹 프로젝트를 배포할 때 WebJob으로 자동 배포되도록 구성합니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

* [웹 프로젝트를 제외하고 배포](#convertnolink).

  기존 콘솔 애플리케이션 프로젝트가 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 애플리케이션 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> 웹 프로젝트와 함께 자동 WebJob 배포 사용

1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)** 을 클릭합니다.
   
    ![Existing Project as Azure WebJob(기존 프로젝트를 Azure WebJob으로)](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. **프로젝트 이름** 드롭다운 목록에서 WebJob으로 추가할 콘솔 애플리케이션 프로젝트를 선택합니다.
   
    ![Add Azure WebJob(Azure WebJob 추가) 대화 상자에서 프로젝트 선택](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> 웹 프로젝트를 제외한 WebJob 배포 사용
1. **솔루션 탐색기**에서 콘솔 애플리케이션 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Azure WebJob 게시...** 를 클릭합니다. 
   
    ![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [프로젝트 이름](#configure) 상자에서 프로젝트가 선택된 상태로 **Add Azure WebJob(Azure WebJob 추가)** 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.
   
   **웹 게시** 마법사가 나타납니다.  바로 게시하지 않으려면 마법사를 닫습니다. 입력한 설정은 [프로젝트를 배포](#deploy)할 때 사용할 수 있게 저장됩니다.

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>새 WebJob 지원 프로젝트 만들기
새 WebJob 지원 프로젝트를 만들려면 [이전 섹션](#convert)에 설명된 대로 콘솔 애플리케이션 프로젝트 템플릿을 사용하고 WebJob 배포를 사용하도록 설정할 수 있습니다. 또는 WebJob new-project 템플릿을 사용할 수도 있습니다.

* [독립적인 WebJob에 웹작업 새 프로젝트 템플릿 사용](#createnolink)
  
    프로젝트를 만든 다음 웹 프로젝트에 연결되지 않고 WebJob 자체로 배포되도록 구성합니다. 웹 앱에서 웹 애플리케이션은 실행하지 않고 WebJob만 실행하려는 경우에 이 옵션을 사용합니다. 웹 애플리케이션 리소스와는 별도로 WebJob 리소스를 확장하기 위해서도 이러한 방식을 원할 수 있습니다.
* [웹 프로젝트에 연결된 WebJob에 대해 WebJobs 새 프로젝트 템플릿 사용](#createlink)
  
    동일한 솔루션의 웹 프로젝트가 배포될 때 자동으로 WebJob으로 배포되도록 구성된 프로젝트를 만듭니다. 관련 웹 애플리케이션을 실행하는 동일한 웹 앱에서 WebJob을 실행하려는 경우에 이 옵션을 사용합니다.

> [!NOTE]
> WebJobs new-project 새 프로젝트 템플릿은 NuGet 패키지를 자동으로 설치하고 *WebJobs SDK* 에 대한 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)에 코드를 포함합니다. WebJobs SDK를 사용하지 않으려면 *Program.cs*에서 `host.RunAndBlock` 문을 제거하거나 변경합니다.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 독립 WebJob을 위해 WebJob new-project 템플릿 사용
1. **새 프로젝트** **파일을** > 클릭한 다음 **새 프로젝트** 대화 상자에서 **클라우드** > **Azure WebJob(.NET 프레임워크)을**클릭합니다.
   
    ![WebJob 템플릿을 표시하는 새 프로젝트 대화 상자](./media/webjobs-dotnet-deploy-vs/np.png)
2. 앞에 표시된 지침에 따라 [콘솔 애플리케이션 프로젝트를 독립 WebJob 프로젝트로 만듭니다](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> 웹 프로젝트에 연결된 WebJob을 위해 WebJob new-project 템플릿 사용
1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **New Azure WebJob Project(새 Azure WebJob 프로젝트)** 를 클릭합니다.
   
    ![New Azure WebJob Project(새 Azure WebJob 프로젝트) 메뉴 항목](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자가 나타납니다.
2. [Add Azure WebJob(Azure WebJob 추가)](#configure) 대화 상자를 완료하고 **확인**을 클릭합니다.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Azure WebJob 추가 대화 상자
**Azure WebJob 추가** 대화 상자에서 WebJob 이름을 입력하고 WebJob에 대한 모드 설정을 실행할 수 있습니다. 

![Add Azure WebJob(Azure WebJob 추가) 대화 상자](./media/webjobs-dotnet-deploy-vs/aaw2.png)

이 대화 상자의 필드는 Azure Portal의 **WebJob 추가** 대화 상자에 있는 필드에 해당합니다. 자세한 내용은 [WebJobs로 백그라운드 작업 실행](webjobs-create.md)을 참조하세요.

> [!NOTE]
> * 명령줄 배포에 대한 자세한 내용은 [Azure WebJob의 명령줄 또는 지속적인 전송 사용](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)을 참조하세요.
> * WebJob을 배포한 다음 WebJob의 유형을 변경하고 재배포하는 경우, *webjobs-publish-settings.json* 파일을 삭제해야 합니다. 이렇게 하면 Visual Studio가 게시 옵션을 다시 표시하므로, 웹 작업의 유형을 변경할 수 있습니다.
> * WebJob을 배포하고 나중에 실행 모드를 연속에서 비연속으로 또는 그 반대로 변경하면 Visual Studio는 사용자가 WebJob을 다시 배포할 때 Azure에서 새 WebJob을 만듭니다. 다른 일정 설정을 변경하고 실행 모드를 그대로 두거나 예약형 및 주문형 간을 전환하면 Visual Studio는 새 작업을 만들지 않고 기존 작업을 업데이트합니다.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
WebJob 배포를 위해 콘솔 애플리케이션을 구성하는 경우, Visual Studio는 [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 패키지를 설치하고 WebJob 프로젝트의 프로젝트 *Properties* 폴더에 있는 *webjob-publish-settings.json* 파일에 일정 정보를 저장합니다. 다음은 이 파일의 예입니다.

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마가 저장되고 [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) 거기에서 볼 수 있습니다.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
WebJob 지원 프로젝트를 웹 프로젝트에 연결하면 Visual Studio는 WebJob 프로젝트의 이름을 웹 프로젝트의 *Properties* 폴더에 있는 *webjobs-list.json* 파일에 저장합니다. 이 목록에는 다음 예와 같은 여러 WebJob 프로젝트가 포함될 수 있습니다.

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

이 파일을 직접 편집할 수도 있고 Visual Studio에 제공되는 IntelliSense를 사용할 수도 있습니다. 파일 스키마가 저장되고 [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) 거기에서 볼 수 있습니다.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>WebJob 프로젝트 배포
웹 프로젝트에 연결한 WebJob 프로젝트는 웹 프로젝트와 함께 자동으로 배포됩니다. 웹 프로젝트 배포에 대한 자세한 내용은 왼쪽 탐색에서 > **앱 배포** 방법 **안내서를 참조하세요.**

WebJob 프로젝트 자체적으로 배포하려면 **솔루션 탐색기**에서 이 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure WebJob 게시...** 를 클릭합니다. 

![Publish as Azure WebJob(Azure WebJob으로 게시)](./media/webjobs-dotnet-deploy-vs/paw.png)

독립 WebJob의 경우 웹 프로젝트에 사용되는 것과 동일한 **웹 게시** 마법사가 나타나지만 변경할 수 있는 설정은 더 적습니다.

## <a name="scheduling-a-triggered-webjob"></a>트리거형 웹 작업 일정 예약

WebJobs는 *settings.job* 파일을 사용하여 WebJob이 실행되는 시기를 결정합니다. 이 파일을 사용하여 WebJob에 대한 실행 일정을 설정합니다. 다음 예제는 오전 9시부터 오후 5시까지 매시간 실행됩니다.

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

이 파일은 WebJobs 폴더의 루트에 위치해야 하며, WebJob의 스크립트(예: `wwwroot\app_data\jobs\triggered\{job name}` 또는 `wwwroot\app_data\jobs\continuous\{job name}`)와 함께 있어야 합니다. Visual Studio에서 WebJob을 배포하는 경우 `settings.job` 파일 속성을 **변경된 내용만 복사**로 표시합니다. 

Azure [포털에서 WebJob을 만들](webjobs-create.md)때 settings.job 파일이 만들어집니다.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 식

WebJobsAzure Functions에서 타이머 트리거로 예약에 동일한 CRON 식을 사용 합니다. CRON 지원에 대한 자세한 내용은 [타이머 트리거 참조 문서를](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)참조하십시오.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>설정.작업 참조

다음 설정은 WebJobs에서 지원됩니다.

| **설정** | **유형**  | **설명** |
| ----------- | --------- | --------------- |
| `is_in_place` | 모두 | 임시 폴더에 먼저 복사하지 않고 작업을 실행합니다. 자세한 내용은 [WebJobs 작업 디렉터리를](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)참조하십시오. |
| `is_singleton` | 연속 | 확장이 완료되면 단일 인스턴스에서만 WebJobs를 실행합니다. 자세한 내용은 [연속 작업 설정을 단일 톤으로 참조하세요.](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton) |
| `schedule` | 트리거 | CRON 기반 일정에 따라 WebJob을 실행합니다. 자세한 내용은 타이머 [트리거 참조 문서를](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)참조하십시오. |
| `stopping_wait_time`| 모두 | 종료 동작을 제어할 수 있습니다. 자세한 내용은 [정상 종료](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)를 참조하십시오. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)
