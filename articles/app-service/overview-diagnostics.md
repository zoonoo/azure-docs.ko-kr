---
title: 진단 및 해결 도구
description: Azure 포털의 진단 및 해결 도구를 사용하여 Azure 앱 서비스에서 앱문제를 해결하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 진단, 지원, 웹앱, 문제 해결, 자가 진단
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: e06e71d4436ac6c64ff2edc876d7849d084482f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671629"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 진단 개요

웹 애플리케이션을 실행할 때 500개 오류부터 사용자가 사이트의 작동이 중단되었음을 알리는 사용자에 이르기까지 발생할 수 있는 문제에 대비해야 합니다. 앱 서비스 진단은 구성없이 앱 문제를 해결하는 데 도움이 되는 지능적이고 대화형 환경입니다. 앱에 문제가 발생하면 App Service 진단에서 올바른 정보로 안내하여 문제를 보다 쉽고 빠르게 해결하고 해결하는 데 문제가 있다고 지적합니다.

이 환경은 지난 24시간 이내에 앱에 문제가 있을 때 가장 유용하지만 모든 진단 그래프를 분석할 수 있습니다.

App Service 진단은 Windows의 앱 뿐만 아니라 [Linux/컨테이너](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service 환경](https://docs.microsoft.com/azure/app-service/environment/intro) 및 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)의 앱에도 작용합니다.

## <a name="open-app-service-diagnostics"></a>App Service 진단 열기

앱 서비스 진단에 액세스하려면 [Azure 포털에서](https://portal.azure.com)앱 서비스 웹 앱 또는 앱 서비스 환경으로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다.

Azure Functions의 경우 함수 앱으로 이동하여 상단 탐색에서 **플랫폼 기능을**클릭하고 **리소스 관리** 섹션에서 문제 진단 **및 해결을** 선택합니다.

앱 서비스 진단 홈페이지에서 각 홈페이지 타일의 키워드를 사용하여 앱의 문제를 가장 잘 설명하는 범주를 선택할 수 있습니다. 또한 이 페이지에서는 Windows 앱용 **진단 도구를** 찾을 수 있습니다. [(Windows 앱에만) 진단 도구를](#diagnostic-tools-only-for-windows-app)참조하십시오.

![홈페이지](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>대화형 인터페이스

앱의 문제에 가장 잘 맞는 홈페이지 카테고리를 선택하면 앱 서비스 진단의 대화형 인터페이스인 Genie가 앱에서 문제를 진단하고 해결하는 데 안내할 수 있습니다. 지니에서 제공하는 타일 바로 가기를 사용하여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기는 진단 메트릭에 액세스하는 직접적인 방법을 제공합니다.

![타일 바로 가기](./media/app-service-diagnostics/tile-shortcuts-2.png)

이러한 타일을 클릭하면 타일에 설명된 문제와 관련된 항목 목록을 볼 수 있습니다. 이러한 항목은 전체 보고서의 주목할 만한 정보의 조각을 제공합니다. 이러한 항목 중 한 가지를 클릭하여 문제를 자세히 조사할 수 있습니다. 또한 전체 보고서 **보기를** 클릭하여 한 페이지의 모든 주제를 탐색할 수 있습니다.

![토픽](./media/app-service-diagnostics/application-logs-insights-3.png)

![전체 보고서 보기](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>진단 보고서

주제를 클릭하여 문제를 추가로 조사하도록 선택한 후 그래프와 마크다운으로 보완되는 주제에 대한 자세한 내용을 볼 수 있습니다. 진단 보고서는 앱의 문제를 정확히 파악하는 강력한 도구가 될 수 있습니다.

![진단 보고서](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>상태 검사

앱에 무엇이 잘못되었는지 모르거나 문제 해결을 시작할 위치를 모르는 경우 상태 검진을 시작하는 것이 좋습니다. 상태 진단은 응용 프로그램을 분석하여 건강 상태와 문제점을 지적하는 빠르고 대화형 개요를 제공하여 문제를 조사할 위치를 알려줍니다. 지능적인 대화형 인터페이스는 문제 해결 프로세스를 통해 지침을 제공합니다. 상태 진단은 Windows 앱용 지니 환경 및 Linux 앱용 웹 앱 다운 진단 보고서와 통합됩니다.

### <a name="health-checkup-graphs"></a>상태 진단 그래프

건강 검진에는 네 가지 그래프가 있습니다.

- **요청 및 오류:** HTTP 서버 오류와 함께 지난 24시간 동안 이루어진 요청 수를 보여 주는 그래프입니다.
- **앱 성능:** 다양한 백분위수 그룹에 대한 지난 24시간 동안의 응답 시간을 보여 주는 그래프입니다.
- **CPU 사용량:** 지난 24시간 동안 인스턴스당 전체 CPU 사용량을 보여 주는 그래프입니다.  
- **메모리 사용량:** 지난 24시간 동안 인스턴스당 전체 실제 메모리 사용량을 보여 주는 그래프입니다.

![상태 검사](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>응용 프로그램 코드 문제 조사(Windows 앱에만)

많은 앱 문제가 애플리케이션 코드의 문제와 관련되어 있으므로 App Service 진단은 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)와 통합되어 예외 및 종속성 문제를 강조 표시하고, 선택된 작동 중단 시간과의 상관 관계를 표시합니다. 응용 프로그램 인사이트는 별도로 활성화해야 합니다.

![애플리케이션 정보](./media/app-service-diagnostics/application-insights-7.png)

Application Insights 예외 및 종속성을 보려면 **웹 앱 아래로** 또는 웹 앱 **느린** 타일 바로 가기를 선택합니다.

### <a name="troubleshooting-steps-only-for-windows-app"></a>문제 해결 단계(Windows 앱에만)

지난 24시간 이내에 특정 문제 범주에서 문제가 발견되면 전체 진단 보고서를 볼 수 있으며 App Service 진단에서는 더 많은 문제 해결 조언과 다음 단계를 확인하라는 메시지가 표시될 수 있습니다.

![애플리케이션 인사이트 및 문제 해결 및 다음 단계](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>진단 도구(Windows 앱용)

진단 도구에는 응용 프로그램 코드 문제, 속도 저하, 연결 문자열 등을 조사하는 데 도움이 되는 고급 진단 도구가 포함되어 있습니다. CPU 사용량, 요청 및 메모리 문제를 완화하는 데 도움이 되는 사전 예방적 도구입니다.

### <a name="proactive-cpu-monitoring"></a>사전 CPU 모니터링

사전 예방적 CPU 모니터링을 사용하면 앱의 앱 또는 자식 프로세스가 높은 CPU 리소스를 소비할 때 쉽고 사전 예방적인 조치를 취할 수 있습니다. 예기치 않은 문제의 실제 원인이 발견될 때까지 고유한 CPU 임계값 규칙을 설정하여 높은 CPU 문제를 일시적으로 완화할 수 있습니다. 자세한 내용은 [CPU 문제가 발생하기 전에 완화를](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)참조하십시오.

![사전 CPU 모니터링](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>자동 치유 및 사전 자동 치유

자동 복구는 앱에 예기치 않은 동작이 있을 때 수행할 수 있는 완화 작업입니다. 요청 수, 느린 요청, 메모리 제한 및 HTTP 상태 코드에 따라 사용자 고유의 규칙을 설정하여 완화 작업을 트리거할 수 있습니다. 근본 원인을 찾을 때까지 도구를 사용하여 예기치 않은 동작을 일시적으로 완화합니다. 자세한 내용은 [앱 서비스 진단에서 새로운 자동 복구 환경 발표를](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)참조하십시오.

![자동 치유](./media/app-service-diagnostics/auto-healing-10.png)

사전 CPU 모니터링과 마찬가지로 사전 자동 복구는 앱의 예기치 않은 동작을 완화하는 턴키 솔루션입니다. 앱 서비스에서 앱이 복구할 수 없는 상태에 있다고 판단하면 사전 자동 복구가 앱을 다시 시작합니다. 자세한 내용은 [사전 자동 복구 소개를](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)참조하십시오.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>네비게이터 및 변경 분석(Windows 앱용)

지속적인 통합이 있고 앱에 많은 종속성이 있는 대규모 팀에서는 비정상 동작을 일으키는 특정 변경 사항을 파악하기 어려울 수 있습니다. Navigator는 앱의 종속성 맵과 동일한 구독의 모든 리소스를 자동으로 렌더링하여 앱의 토폴로지에서 가시성을 확보할 수 있도록 도와줍니다. Navigator를 사용하면 앱 및 해당 종속성에 의해 변경된 내용의 통합 목록을 보고 비정상 동작을 일으키는 변경 내용을 좁힐 수 있습니다. 그것은 홈페이지 타일 **네비게이터를** 통해 액세스 할 수 있으며 처음 사용하기 전에 활성화해야합니다. 자세한 내용은 [Navigator를 통해 앱의 종속성에 대한 가시성 얻기를 참조하세요.](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)

![네비게이터 기본 페이지](./media/app-service-diagnostics/navigator-default-page-11.png)

![디프 뷰](./media/app-service-diagnostics/diff-view-12.png)

앱 변경에 대한 변경 분석은 타일 바로 가기, **응용 프로그램 변경** 및 가용성 및 **성능의** **응용 프로그램 충돌을** 통해 액세스할 수 있으므로 다른 메트릭과 동시에 사용할 수 있습니다. 이 기능을 사용하기 전에 먼저 활성화해야 합니다. 자세한 내용은 [앱 서비스 진단에서 새 변경 분석 환경 발표를](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)참조하십시오.

제목에 "[Diag]"를 추가하여 [UserVoice에](https://feedback.azure.com/forums/169385-web-apps) 질문이나 피드백을 게시합니다.
