---
title: "Microsoft Azure 모니터링 | Microsoft Docs"
description: "Microsoft Azure에서 모니터링하려는 경우에 선택합니다. Azure Monitor, Application Insights Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure 모니터링 개요
이 문서에서는 Microsoft Azure 모니터링에 사용할 수 있는 도구에 대해 간략히 설명합니다. 적용 대상은 다음과 같습니다. 
- Microsoft Azure에서 실행 중인 응용 프로그램 모니터링 
- Azure 외부에서 실행되어 Azure의 개체를 모니터링할 수 있는 도구/서비스 

사용할 수 있는 다양한 제품과 서비스 및 작동하는 방법에 대해 설명합니다. 어떤 도구가 어떤 경우에 가장 적합한지 판단하는 데 도움이 될 수 있습니다.  

## <a name="why-use-monitoring-and-diagnostics"></a>모니터링 및 진단을 사용하는 이유는?

클라우드 앱의 성능 문제는 비즈니스에 영향을 미칠 수 있습니다. 상호 연결된 여러 구성 요소와 자주 제공되는 릴리스를 사용하면 언제든지 성능 저하가 발생할 수 있습니다. 앱을 개발하는 경우 사용자는 일반적으로 테스트에서 찾지 못한 문제를 발견합니다. 이러한 문제에 대해 즉시 알고 있어야 하며 문제를 진단하고 수정하는 도구가 있어야 합니다. Microsoft Azure에는 이러한 문제를 식별할 수 있는 다양한 도구가 있습니다.

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>Azure 클라우드 앱을 모니터링하는 방법은?

Azure 응용 프로그램과 서비스를 모니터링하기 위한 다양한 도구가 있습니다. 일부 기능은 겹칩니다. 이는 부분적으로 역사적인 이유 또는 응용 프로그램의 개발과 운영 사이의 모호함 때문입니다. 

다음은 주요 도구입니다.

-    **Azure Monitor** - Azure에서 실행되는 서비스를 모니터링하는 기본 도구입니다. 또한 서비스의 처리량과 주변 환경에 대한 인프라 수준 데이터를 제공합니다. Azure에서 모든 앱을 관리하고 리소스를 확장 또는 축소할지 여부를 결정하면, Azure Monitor에서 시작하는 데 사용할 데이터를 제공합니다.

-    **Application Insights** - 개발을 위해 사용하거나 프로덕션 모니터링 솔루션으로 사용할 수 있습니다. 앱에 패키지를 설치하여 작동하므로 진행 상황에 대한 자세한 내부 보기를 제공합니다. 이 데이터에는 종속성, 예외 추적, 디버깅 스냅숏, 실행 프로필의 응답 시간이 포함됩니다. 이 원격 분석 데이터를 모두 분석하여 앱을 디버그하고 사용자가 무엇을 하고 있는지 이해할 수 있도록 도움을 주는 강력한 스마트 도구를 제공합니다. 응답 시간의 급증이 어떤 앱 요소 또는 어떤 외부 리소스 문제로 인한 것인지 여부를 알 수 있습니다. Visual Studio를 사용하고 응용 프로그램에 문제가 있는 경우 문제를 해결할 수 있도록 문제의 코드 줄로 바로 이동할 수 있습니다.  

-    **Log Analytics** - 성능을 튜닝하고 프로덕션 환경에서 실행되는 응용 프로그램의 유지 관리를 계획해야 하는 사용자를 위한 것입니다. 이는 Azure에 기반을 두고 있습니다. 10-15분 간의 지연에도 불구하고 많은 소스의 데이터를 수집하고 집계합니다. Azure, 온-프레미스 및 타사 클라우드 기반 인프라(예: Amazon Web Services)에 대한 전체적인 IT 관리 솔루션을 제공합니다. 더 많은 소스에서 데이터를 분석할 수 있는 다양한 도구를 제공하고, 모든 로그에서 복잡한 쿼리를 허용하며, 지정된 조건에 대해 사전에 경고할 수 있습니다.  사용자 지정 데이터를 해당되는 중앙의 리포지토리에 수집하여 쿼리하고 시각화할 수도 있습니다. 

-    **SCOM(System Center Operations Manager)** - 대규모 클라우드 설치를 관리하고 모니터링하기 위한 것입니다. 온-프레미스 Windows Sever 및 Hyper-V 기반 클라우드를 위한 관리 도구로 이미 친숙해 있을 수 있지만, Azure 앱과 통합하여 이러한 앱을 관리할 수도 있습니다. 무엇보다도 기존의 라이브 앱에 Application Insights를 설치할 수 있습니다.  앱 작동이 중단되면 수 초 안에 알려줍니다. Log Analytics는 SCOM을 대체하지 않습니다. 오히려 함께 결합하여 잘 작동합니다.  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 액세스
모든 Azure 모니터링 서비스는 이제 하나의 UI 창에서 사용할 수 있습니다. 이 영역에 액세스하는 방법에 대한 자세한 내용은 [Azure Monitor 시작](monitoring-get-started.md)을 참조하세요. 

또한 특정 리소스를 강조 표시하고 모니터링 옵션을 드릴다운하여 해당 리소스에 대한 모니터링 기능에 액세스할 수 있습니다. 

## <a name="examples-of-when-to-use-which-tool"></a>어떤 도구를 사용하는 경우의 예 

다음 섹션에서는 몇 가지 기본 시나리오와 함께 사용해야 하는 도구를 보여 줍니다. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>시나리오 1 - 개발 중인 Azure 응용 프로그램의 오류 수정   

**가장 좋은 방법은 Application Insights, Azure Monitor 및 Visual Studio를 함께 사용하는 것입니다.**

이제 Azure는 클라우드에서 Visual Studio 디버거의 모든 기능을 제공합니다. Application Insights에 원격 분석을 보내도록 Azure Monitor를 구성합니다. 응용 프로그램에서 Application Insights SDK를 포함하도록 Visual Studio를 사용하도록 설정합니다. Application Insights에서 Application Map을 사용하여 실행 중인 응용 프로그램의 어느 요소가 비정상인지 여부를 시각적으로 검색할 수 있습니다. 정상이 아닌 요소의 경우 이미 오류 및 예외가 탐색에 사용될 수 있습니다. Application Insights에서 다양한 분석을 사용하여 더 깊이 진행할 수 있습니다. 오류에 대해 확실하지 않은 경우 Visual Studio 디버거를 사용하여 코드를 추적함으로써 문제를 정확히 파악할 수 있습니다. 

자세한 내용은 [Web Apps 모니터링](../application-insights/app-insights-azure-web-apps.md)을 참조하고, 왼쪽의 목차를 통해 다양한 종류의 응용 프로그램과 언어에 대한 지침을 참조하세요.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>시나리오 2 - 프로덕션 환경에서만 표시되는 오류에 대해 Azure .NET 웹 응용 프로그램 디버깅 

> [!NOTE]
> 이러한 기능은 미리 보기에 있습니다. 

**가장 좋은 방법은 Application Insights 및 완벽한 디버깅 환경을 위해 가능한 경우 Visual Studio를 사용하는 것입니다.**

Application Insights 스냅숏 디버거를 사용하여 앱을 디버그합니다. 특정 오류 임계값이 프로덕션 구성 요소에서 발생하면 시스템에서 "스냅숏"이라고 하는 시간 창의 원격 분석을 자동으로 캡처합니다. 캡처된 양은 성능에 영향을 미치지 않을 만큼 작지만 추적을 허용할 만큼 충분히 크기 때문에 프로덕션 클라우드에서는 안전합니다.  시스템에서 여러 개의 스냅숏을 캡처할 수 있습니다. Azure Portal에서 특정 시점을 확인하거나 완벽한 환경을 위해 Visual Studio를 사용할 수 있습니다. Visual Studio를 사용하면 개발자는 실시간으로 디버그하는 것처럼 스냅숏을 단계별로 진행할 수 있습니다. 로컬 변수, 매개 변수, 메모리 및 프레임을 모두 사용할 수 있습니다. 개발자는 RBAC 역할을 통해 이 프로덕션 데이터에 대한 액세스 권한을 부여받아야 합니다.  

자세한 내용은 [스냅숏 디버깅](../application-insights/app-insights-snapshot-debugger.md)을 참조하세요. 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>시나리오 3 - 컨테이너 또는 마이크로 서비스를 사용하는 Azure 응용 프로그램 디버깅 

**시나리오 1과 동일합니다. Application Insights, Azure Monitor 및 Visual Studio를 함께 사용합니다.** Application Insights는 컨테이너 내 및 마이크로 서비스(Kubernetes, Docker, Azure Service Fabric)에서 실행되는 프로세스로부터 원격 분석을 수집하는 것도 지원합니다. 자세한 내용은 [여기에 있는 컨테이너 및 마이크로 서비스 디버깅에 대한 비디오를 참조하세요](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>시나리오 4 - Azure 응용 프로그램의 성능 문제 해결

[Application Insights 프로파일러](../application-insights/app-insights-profiler.md)는 이러한 유형의 문제를 해결하는 데 도움을 주도록 설계되었습니다. App Services(Web Apps, Logic Apps, Mobile Apps, API Apps) 및 다른 계산 리소스(예: Virtual Machines, VMSS(Virtual Machine Scale Sets), Cloud Services 및 Service Fabric)에서 실행되는 응용 프로그램의 성능 문제를 식별하고 해결할 수 있습니다. 

> [!NOTE]
> Virtual Machines, VMSS, Cloud Services 및 Services Fabric 프로파일링 기능은 미리 보기에 있습니다.   

또한 스마트 검색 도구를 사용하여 느린 페이지 로드 속도와 같은 특정 유형의 오류에 대해 전자 메일을 통해 사전에 알려줍니다.  이 도구에서 구성을 수행할 필요는 없습니다. 자세한 내용은 [스마트 검색 - 성능 이상](../application-insights/app-insights-proactive-performance-diagnostics.md) 및 [스마트 검색 - 성능 이상](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview)을 참조하세요.



## <a name="next-steps"></a>다음 단계
자세한 정보

* [Ignite 2016의 비디오에서 Azure Monitor](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor 시작](monitoring-get-started.md)
* [Azure 진단](../azure-diagnostics.md) - 클라우드 서비스, 가상 컴퓨터, 가상 컴퓨터 확장 집합 또는 Service Fabric 응용 프로그램에서 문제를 진단하려는 경우
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) - 앱 서비스 웹앱에서 문제를 진단하려는 경우
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 및 [Operations Management Suite](https://www.microsoft.com/oms/) 프로덕션 모니터링 솔루션
