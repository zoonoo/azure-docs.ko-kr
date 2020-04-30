---
title: Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure App Service에 라이브 앱을 프로파일링합니다.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275777"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링

ASP.NET에서 Profiler를 실행 하 고 기본 서비스 계층 이상을 사용 하 여 Azure App Service에서 실행 되는 앱을 ASP.NET Core 수 있습니다. Linux에서 Profiler를 사용하도록 설정하려면 반드시 [이 방법](profiler-aspnetcore-linux.md)을 사용해야 합니다.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 앱에 대해 Profiler 사용
앱에 대해 Profiler를 사용하도록 설정하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 프로파일러를 사용 하도록 설정 하는 지침은 다음과 같습니다.
* [클라우드 서비스](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 애플리케이션](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler App Services 런타임의 일부로 미리 설치 됩니다. 아래 단계에서는 App Service에 대해이 기능을 사용 하도록 설정 하는 방법을 보여 줍니다. 빌드 시 응용 프로그램에 App Insights SDK를 포함 한 경우에도 다음 단계를 수행 합니다.

1. App service에 대해 "Always On" 설정을 사용 하도록 설정 합니다. 일반 설정에서 App Service의 구성 페이지에서 설정을 업데이트할 수 있습니다.
1. Azure Portal에서 **App Services** 창으로 이동합니다.
1. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/profiler/AppInsights-AppServices.png)

1. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 Profiler가 **켜기** 상태인지 확인합니다. Application Insights 리소스가 App Service와 다른 구독에 있는 경우이 페이지를 사용 하 여 Application Insights을 구성할 수 없습니다. 필요한 앱 설정을 수동으로 만들어 수동으로 수행할 수 있습니다. [다음 섹션에는 수동으로 프로파일러를 사용 하도록 설정 하는 지침이 포함 되어 있습니다.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights 사이트 확장 추가][Enablement UI]

1. 이제 Profiler가 App Services 앱 설정을 사용하여 활성화됩니다.

    ![Profiler에 대한 앱 설정][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>수동으로 프로파일러를 사용 하도록 설정 Azure Resource Manager
Azure App Service에 대 한 앱 설정을 만들어 Application Insights Profiler를 사용 하도록 설정할 수 있습니다. 위에 표시 된 옵션을 사용 하는 페이지는 이러한 앱 설정을 만듭니다. 그러나 템플릿이나 다른 방법을 사용 하 여 이러한 설정의 생성을 자동화할 수 있습니다. 이러한 설정은 Application Insights 리소스가 Azure App Service와 다른 구독에 있는 경우에도 작동 합니다.
프로파일러를 사용 하도록 설정 하는 데 필요한 설정은 다음과 같습니다.

|앱 설정    | 값    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스의 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


[Azure Resource Manager 템플릿](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)를 사용 하 여 이러한 값을 설정할 수 있습니다.

### <a name="enabling-profiler-for-other-clouds-manually"></a>수동으로 다른 클라우드에 대해 프로파일러 사용

다른 클라우드에 대해 프로파일러를 사용 하도록 설정 하려는 경우 아래 앱 설정을 사용할 수 있습니다.

|앱 설정    | 미국 정부 값| 중국 클라우드 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Profiler 사용 안 함

개별 앱의 인스턴스에 대해 Profiler를 중지하거나 다시 시작하려면 **Web Jobs**에서 앱 리소스로 이동합니다. Profiler를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 Profiler 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 앱에서 Profiler를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용 하 여 웹 응용 프로그램에 변경 내용을 배포 하는 경우 Profiler의 파일을 삭제할 수 있습니다. 배포 하는 동안 삭제 되지 않도록 App_Data 폴더를 제외 하 여 삭제를 방지할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [Visual Studio Online에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
