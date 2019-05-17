---
title: Azure App Service 진단 개요 | Microsoft Docs
description: App Service 진단 사용 하 여 앱을 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
keywords: App Service, Azure App Service, 진단, 지원, 웹앱, 문제 해결, 자가 진단
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539909"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 진단 개요

웹 애플리케이션을 실행할 때 500개 오류부터 사용자가 사이트의 작동이 중단되었음을 알리는 사용자에 이르기까지 발생할 수 있는 문제에 대비해야 합니다. App Service 진단은 앱 구성이 필요 없는 문제 해결 하는 데는 지능적인 대화형 환경입니다. 앱을 사용 하 여 문제를 실행 하 고 수행 하는 경우 App Service 진단 무엇이 더 쉽고 빠르게 문제를 해결 하 고 문제를 해결 하는 데 적합 한 정보로 안내 하는 요소입니다.

경우에이 환경을 가장 유용한 경우 문제가 발생 하는 앱을 사용 하 여 지난 24 시간 동안 모든 진단 그래프가 항상 사용할 분석할 수 있도록 합니다.

App Service 진단은 Windows의 앱 뿐만 아니라 [Linux/컨테이너](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service 환경](https://docs.microsoft.com/azure/app-service/environment/intro) 및 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)의 앱에도 작용합니다.

## <a name="open-app-service-diagnostics"></a>App Service 진단 열기

App Service 진단에 액세스 하려면 App Service 웹 앱 또는 App Service Environment에서로 이동 합니다 [Azure portal](https://portal.azure.com)합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다.

Azure functions의 위쪽 탐색 모음에 함수 앱으로 이동, 클릭 **플랫폼 기능**, 선택한 **진단 및 문제 해결** 에서 **리소스관리** 섹션입니다.

App Service 진단 홈 페이지에서 각 홈 페이지 타일에서 키워드를 사용 하 여 앱을 사용 하 여 문제를 설명 하는 범주를 선택할 수 있습니다. 또한이 페이지를 찾을 수 있습니다 **진단 도구** Windows 앱에 대 한 합니다. 참조 [진단 도구 (Windows 앱)에 해당](#diagnostic-tools-only-for-windows-app)합니다.

![홈 페이지](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>대화형 인터페이스

앱의 문제를 가장 잘 부합 하는 홈 페이지 범주를 선택 하면 App Service 진단 대화형 인터페이스를 지니는, 수 안내 진단 하 고 앱을 사용 하 여 문제를 해결 합니다. 관심 있는 문제 범주에 속하는 전체 진단 보고서를 보려면 지니는 제공한 타일 바로 가기를 사용할 수 있습니다. 타일 바로 가기를 진단 메트릭에 액세스할 수 있는 직접적인 방법을 제공 합니다.

![타일 바로 가기](./media/app-service-diagnostics/tile-shortcuts-2.png)

이러한 타일을 클릭 하면 타일에 설명 된 문제와 관련 된 항목의 목록을 볼 수 있습니다. 이러한 항목은 전체 보고서에서 주목할 만한 정보 조각을 제공합니다. 추가로 문제를 조사 하려면 다음이 항목 중 하나에서 클릭할 수 있습니다. 또한을 클릭할 수 있습니다 **전체 보고서 보기** 단일 페이지에 있는 모든 항목을 소개 합니다.

![주제](./media/app-service-diagnostics/application-logs-insights-3.png)

![전체 보고서 보기](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>진단 보고서

항목을 클릭 하 여 문제를 자세히 조사 하려면을 선택 하면 종종 그래프 및 markdown을 사용 하 여 보완 항목에 대 한 자세한 세부 정보를 볼 수 있습니다. 진단 보고서는 앱을 사용 하 여 문제를 정확히 찾아내기 위한 강력한 도구를 수 있습니다.

![진단 보고서](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>상태 검사

앱을 사용 하 여 무엇이 알지 하거나 문제를 해결 하려면 위치를 알 수 없는 경우 상태 검사는 시작 하는 것이 좋습니다. 상태 검사는 정상 및 문제를 조사 하려면 찾으려는 위치를 알려주는 문제를 지적 하는 빠른 대화형 개요를 제공 하도록 응용 프로그램을 분석 합니다. 지능적인 대화형 인터페이스는 문제 해결 프로세스를 통해 지침을 제공합니다. Windows 앱 및 웹 앱 진단 아래쪽에 대 한 상태 검사 지니는 환경과 통합 되어 Linux 앱에 대 한 보고서입니다.

### <a name="health-checkup-graphs"></a>상태 점검 그래프

상태 검사에 네 개의 다른 그래프가 있습니다.

- **요청 및 오류:** HTTP 서버 오류와 함께 최근 24 시간 동안의 요청 수를 보여 주는 그래프입니다.
- **앱 성능:** 다양 한 백분위 수 그룹에 대 한 지난 24 시간 응답 시간을 보여 주는 그래프입니다.
- **CPU 사용량:** 지난 24 시간 동안 인스턴스당 전체 백분율 CPU 사용량을 보여 주는 그래프입니다.  
- **메모리 사용량:** 지난 24 시간 동안 인스턴스당 백분율 전체 실제 메모리 사용량을 보여 주는 그래프입니다.

![상태 검사](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>(Windows 앱)에 해당 응용 프로그램 코드 문제를 조사 합니다.

많은 앱 문제가 애플리케이션 코드의 문제와 관련되어 있으므로 App Service 진단은 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)와 통합되어 예외 및 종속성 문제를 강조 표시하고, 선택된 작동 중단 시간과의 상관 관계를 표시합니다. Application Insights는 별도로 사용 하도록 설정 해야 합니다.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Application Insights 예외 및 종속성을 보려면 선택 합니다 **웹 앱 중단** 또는 **느린 웹 앱** 타일 바로 가기.

### <a name="troubleshooting-steps-only-for-windows-app"></a>(Windows 앱)에 문제 해결 단계

문제는 지난 24 시간 동안 특정 문제 범주를 사용 하 여 감지 되 면 전체 진단 보고서를 볼 수 있으며 App Service 진단 보다 문제 해결 도움말 및 자세한 단계별된 환경의 다음 단계를 확인 하도록 요구할 수 있습니다.

![Application Insights 및 문제 해결 및 다음 단계](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>진단 도구 (Windows 앱)에 해당

진단 도구는 응용 프로그램을 조사 하는 도움말 코드 문제, 속도 저하, 연결 문자열 및 기타 정보는 고급 진단 도구를 포함 합니다. 고 지 원하는 사전 도구 CPU 사용량, 요청 및 메모리를 사용 하 여 문제를 완화 합니다.

### <a name="proactive-cpu-monitoring"></a>사전 CPU 모니터링

사전 CPU 모니터링 앱에 대 한 앱 또는 자식 프로세스 높은 CPU 리소스를 소비 하는 경우 작업을 수행 하는 쉽고 자동 관리 방법을 제공 합니다. 예기치 않은 문제에 대 한 실제 원인의 찾을 때까지 높은 CPU 문제를 일시적으로 완화 하기 위해 사용자 고유의 CPU 임계값 규칙을 설정할 수 있습니다.

![사전 CPU 모니터링](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>자동 관리 자동 복구

사전 CPU 모니터링와 같은 자동 관리 자동 복구는 앱의 예기치 않은 동작을 완화 하는 편리 하 고 사전 대응적인 접근 방식을 제공 합니다. 요청 수, 느린 요청, 메모리 제한 및 HTTP 상태 코드 완화 작업 트리거를 기반으로 사용자 고유의 규칙을 설정할 수 있습니다. 이 도구는 문제에 대 한 실제 원인의 찾을 때까지 예기치 않은 동작인을 일시적으로 완화 하기 위해 사용할 수 있습니다. 자동 관리 자동 복구에 대 한 자세한 내용은 방문 [app service 진단에서 환경을 복구 새 자동 발표](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![자동 관리 자동 복구](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>변경 분석

빠른 개발 환경에서 경우도 앱에 대 한 모든 변경 내용을 추적 및 비정상 동작을 발생 시킨 변경에만 pinpoint 사용 하기가 어렵습니다. 변경 분석 문제 해결 경험을 용이 하 게 하려면 앱에 대 한 변경 내용에 범위를 좁힐 수 있습니다. 와 같은 변경 분석 진단 보고서에 포함 된 **응용 프로그램이 크래시 되** 동시 다른 메트릭을 사용할 수 있도록 합니다.

![변경 분석 기본 페이지](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Diff 보기](./media/app-service-diagnostics/diff-view-12.png)

변경 분석 기능을 사용 하기 전에 사용 하도록 설정 해야 합니다. 변경 분석에 대 한 자세한 내용은 방문 [App Service 진단의 새로운 변경 분석 환경 발표](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)합니다.